---
title: "[Note] 25-03-27 AMLworlkd_code.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-03-27 21:49:00 +0900
comments: false
---
---

/* AMLSim/src/main/java/amlsim/SimProperties.java */
package amlsim;

import java.io.*;
import java.nio.file.*;
import org.json.*;


/**
 * Simulation properties and global parameters loaded from the configuration JSON file
 */
public class SimProperties {

    private static final String separator = File.separator;
    private JSONObject generalProp;
    private JSONObject simProp;
    private JSONObject inputProp;
    private JSONObject outputProp;
    private JSONObject cashInProp;
    private JSONObject cashOutProp;
    private String workDir;
    private double marginRatio;  // Ratio of margin for AML typology transactions
    private int seed;  // Seed of randomness
    private String simName;  // Simulation name

    private int normalTxInterval;
    private double minTxAmount;  // Minimum base (normal) transaction amount
    private double maxTxAmount;  // Maximum base (suspicious) transaction amount

    SimProperties(String jsonName) throws IOException{
        String jsonStr = loadTextFile(jsonName);
        JSONObject jsonObject = new JSONObject(jsonStr);
        JSONObject defaultProp = jsonObject.getJSONObject("default");

        generalProp = jsonObject.getJSONObject("general");
        simProp = jsonObject.getJSONObject("simulator");
        inputProp = jsonObject.getJSONObject("temporal");  // Input directory of this simulator is temporal directory
        outputProp = jsonObject.getJSONObject("output");

        normalTxInterval = simProp.getInt("transaction_interval");
        minTxAmount = defaultProp.getDouble("min_amount");
        maxTxAmount = defaultProp.getDouble("max_amount");

        System.out.printf("General transaction interval: %d\n", normalTxInterval);
        System.out.printf("Base transaction amount: Normal = %f, Suspicious= %f\n", minTxAmount, maxTxAmount);
        
        cashInProp = defaultProp.getJSONObject("cash_in");
        cashOutProp = defaultProp.getJSONObject("cash_out");
        marginRatio = defaultProp.getDouble("margin_ratio");

        String envSeed = System.getenv("RANDOM_SEED");
        seed = envSeed != null ? Integer.parseInt(envSeed) : generalProp.getInt("random_seed");
        System.out.println("Random seed: " + seed);

        simName = System.getProperty("simulation_name");
        if(simName == null){
            simName = generalProp.getString("simulation_name");
        }
        System.out.println("Simulation name: " + simName);

        String simName = getSimName();
        workDir = inputProp.getString("directory") + separator + simName + separator;
        System.out.println("Working directory: " + workDir);
    }

    private static String loadTextFile(String jsonName) throws IOException{
        Path file = Paths.get(jsonName);
        byte[] bytes = Files.readAllBytes(file);
        return new String(bytes);
    }

    String getSimName(){
        return simName;
    }

    public int getSeed(){
        return seed;
    }

    public int getSteps(){
        return generalProp.getInt("total_steps");
    }

    boolean isComputeDiameter(){
        return simProp.getBoolean("compute_diameter");
    }

    int getTransactionLimit(){
        return simProp.getInt("transaction_limit");
    }

    int getNormalTransactionInterval(){
        return normalTxInterval;
    }

    public double getMinTransactionAmount() {
        return minTxAmount;
    }

    public double getMaxTransactionAmount() {
        return maxTxAmount;
    }

    public double getMarginRatio(){
        return marginRatio;
    }

    int getNumBranches(){
        return simProp.getInt("numBranches");
    }

    String getInputAcctFile(){
        return workDir + inputProp.getString("accounts");
    }

    String getInputTxFile(){
        return workDir + inputProp.getString("transactions");
    }

    String getInputAlertMemberFile() {
        return workDir + inputProp.getString("alert_members");
    }

    String getNormalModelsFile() {
        return workDir + inputProp.getString("normal_models");
    }

    String getOutputTxLogFile(){
        return getOutputDir() + outputProp.getString("transaction_log");
    }

    String getOutputDir(){
        return outputProp.getString("directory") + separator + simName + separator;
    }

    String getCounterLogFile(){
        return getOutputDir() + outputProp.getString("counter_log");
    }

    String getDiameterLogFile(){
        return workDir + outputProp.getString("diameter_log");
    }

    int getCashTxInterval(boolean isCashIn, boolean isSAR){
        String key = isSAR ? "fraud_interval" : "normal_interval";
        return isCashIn ? cashInProp.getInt(key) : cashOutProp.getInt(key);
    }

    float getCashTxMinAmount(boolean isCashIn, boolean isSAR){
        String key = isSAR ? "fraud_min_amount" : "normal_min_amount";
        return isCashIn ? cashInProp.getFloat(key) : cashOutProp.getFloat(key);
    }

    float getCashTxMaxAmount(boolean isCashIn, boolean isSAR){
        String key = isSAR ? "fraud_max_amount" : "normal_max_amount";
        return isCashIn ? cashInProp.getFloat(key) : cashOutProp.getFloat(key);
    }
}

/* AMLSim/src/main/java/amlsim/AMLSim.java */

package amlsim;

import amlsim.model.AbstractTransactionModel;
import amlsim.model.ModelParameters;
import amlsim.model.cash.CashInModel;
import amlsim.model.cash.CashOutModel;
import amlsim.model.normal.EmptyModel;
import amlsim.model.normal.FanInTransactionModel;
import amlsim.model.normal.FanOutTransactionModel;
import amlsim.model.normal.ForwardTransactionModel;
import amlsim.model.normal.MutualTransactionModel;
import amlsim.model.normal.PeriodicalTransactionModel;
import amlsim.model.normal.SingleTransactionModel;
import amlsim.model.aml.AMLTypology;
import amlsim.stat.Diameter;
import sim.engine.SimState;

import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;
import java.util.logging.*;

/**
 * AMLSimulator Main class
 */
public class AMLSim extends SimState {

    private static SimProperties simProp;
	private static final int TX_SIZE = 10000000;  // Transaction buffer size
	private static TransactionRepository txs = new TransactionRepository(TX_SIZE);
	private static Logger logger = Logger.getLogger("AMLSim");
//	private static int seed;
	private static Random rand;

	private Map<String, Integer> idMap = new HashMap<>();  // Account ID --> Index
	private Map<Long, Alert> alerts = new HashMap<>();  // Alert ID --> Alert (AML typology) object
	private Map<Long, AccountGroup> accountGroupsMap = new HashMap<>();
	private int numBranches = 0;
	private ArrayList<Branch> branches = new ArrayList<>();
	private int normalTxInterval = 30;  // Default transaction interval for normal accounts
//    private int sarTxInterval = 10;  // Default transaction interval for SAR accounts
//    private float sarBalanceRatio = 10.0F; // Multiplier of initial balance for SAR accounts

	private static String simulatorName = null;
	private ArrayList<String> paramFile = new ArrayList<>();
	private ArrayList<String> actions = new ArrayList<>();
	private ArrayList<Account> accounts = new ArrayList<Account>();
	private BufferedWriter bufWriter;
	private static long numOfSteps = 1;  // Number of simulation steps
	private static int currentLoop = 0;  // Simulation iteration counter
	private static String txLogFileName = "";

	private String accountFile = "";
	private String transactionFile = "";
	private String normalModelsFile = "";
	private String alertMemberFile = "";
	private String counterFile = "";
	private String diameterFile = "";

	private static Diameter diameter;
	private boolean computeDiameter = false;


	private AMLSim(long seed) {
		super(seed);
		AMLSim.rand = new Random(seed);
		Handler handler = new ConsoleHandler();
		logger.addHandler(handler);
		java.util.logging.Formatter formatter = new SimpleFormatter();
		handler.setFormatter(formatter);
        simulatorName = simProp.getSimName();
	}
 
	public static Random getRandom(){
	    return rand;
    }

	public static Logger getLogger(){
	    return logger;
    }

    public static SimProperties getSimProp(){
		return simProp;
	}

	public void setCurrentLoop(int currentLoop){
		AMLSim.currentLoop = currentLoop;
	}

	private List<Account> getAccounts() {
		return this.accounts;
	}
    
    /**
     * Get the number of simulation steps
     * ##return Simulation steps as long
     */
	public static long getNumOfSteps(){
		return numOfSteps;
	}
    
    /**
     * Get an account object from an account ID
     * ##param id Account ID
     * ##return Account object
     */
    private Account getAccountFromID(String id){
		int index = this.idMap.get(id);
		return (Account) this.getAccounts().get(index);
	}
    
    /**
     * Initialize AMLSim by loading account and transaction list files
     */
	public void initSimulation() {
		try {
			loadAccountFile(this.accountFile);
		} catch (IOException e) {
			System.err.println("Cannot load account file: " + this.accountFile);
			e.printStackTrace();
			System.exit(1);
		}

		try {
			loadTransactionFile(this.transactionFile);
		} catch (IOException e) {
			System.err.println("Cannot load transaction file: " + this.transactionFile);
			e.printStackTrace();
			System.exit(1);
		}

		try {
			loadNormalModelsFile(this.normalModelsFile);
		} catch (IOException e) {
			System.err.println("Cannot load normal model file: " + this.normalModelsFile);
			e.printStackTrace();
			System.exit(1);
		}

		try {
			loadAlertMemberFile(this.alertMemberFile);
		} catch (IOException e) {
			System.err.println("Cannot load alert file: " + this.alertMemberFile);
			e.printStackTrace();
			System.exit(1);
		}
	}

	public void loadParametersFromFile() {
		numOfSteps = simProp.getSteps();
		// Default transaction interval for accounts
		this.normalTxInterval = simProp.getNormalTransactionInterval();

		// Number of transactions for logging buffer
        int transactionLimit = simProp.getTransactionLimit();
        if(transactionLimit > 0){  // Set the limit only if the parameter is positive value
            txs.setLimit(transactionLimit);
        }

		// Parameters of Cash Transactions
		int norm_in_int = simProp.getCashTxInterval(true, false);  // Interval of cash-in transactions for normal account
		int suspicious_in_int = simProp.getCashTxInterval(true, true);  // Interval of cash-in transactions for suspicious account
		float norm_in_min = simProp.getCashTxMinAmount(true, false);  // Minimum amount of cash-in transactions for normal account
		float norm_in_max = simProp.getCashTxMaxAmount(true, false);  // Maximum amount of cash-in transactions for normal account
		float suspicious_in_min = simProp.getCashTxMinAmount(true, true);  // Minimum amount of cash-in transactions for suspicious account
		float suspicious_in_max = simProp.getCashTxMaxAmount(true, true);  // Maximum amount of cash-in transactions for suspicious account
		CashInModel.setParam(norm_in_int, suspicious_in_int, norm_in_min, norm_in_max, suspicious_in_min, suspicious_in_max);

		int norm_out_int = simProp.getCashTxInterval(false, false);  // Interval of cash-out transactions for normal account
		int suspicious_out_int = simProp.getCashTxInterval(false, true);  // Interval of cash-out transactions for suspicious account
		float norm_out_min = simProp.getCashTxMinAmount(false, false);  // Minimum amount of cash-out transactions for normal account
		float norm_out_max = simProp.getCashTxMaxAmount(false, false);  // Maximum amount of cash-out transactions for normal account
		float suspicious_out_min = simProp.getCashTxMinAmount(false, true);  // Minimum amount of cash-out transactions for suspicious account
		float suspicious_out_max = simProp.getCashTxMaxAmount(false, true);  // Maximum amount of cash-out transactions for suspicious account
		CashOutModel.setParam(norm_out_int, suspicious_out_int, norm_out_min, norm_out_max, suspicious_out_min, suspicious_out_max);


		// Create branches (for cash transactions)
		this.numBranches = simProp.getNumBranches();
		if(this.numBranches <= 0){
			throw new IllegalStateException("The numBranches must be positive");
		}
		for(int i=0; i<this.numBranches; i++) {
			this.branches.add(new Branch(i));
		}

        this.accountFile = simProp.getInputAcctFile();
        this.transactionFile = simProp.getInputTxFile();
		this.normalModelsFile = simProp.getNormalModelsFile();
        this.alertMemberFile = simProp.getInputAlertMemberFile();
        this.counterFile = simProp.getCounterLogFile();
        this.diameterFile = simProp.getDiameterLogFile();
        this.computeDiameter = simProp.isComputeDiameter();

        if(computeDiameter && diameterFile != null){
            try{
                BufferedWriter writer = new BufferedWriter(new FileWriter(diameterFile));
                writer.close();
            }catch (IOException e){
                e.printStackTrace();
                computeDiameter = false;
            }
            if(computeDiameter){
                logger.info("Compute transaction graph diameters and write them to: " + diameterFile);
            }else{
                logger.info("Transaction graph diameter computation is disabled");
            }
        }
	}


	private static Map<String, Integer> getColumnIndices(String header){
		Map<String, Integer> columnIndex = new HashMap<>();
		String[] element= header.split(",");
		for(int i=0; i<element.length; i++){
			columnIndex.put(element[i], i);
		}
		return columnIndex;
	}
	

	private void loadAccountFile(String accountFile) throws IOException{
		BufferedReader reader = new BufferedReader(new FileReader(accountFile));
		String line = reader.readLine();
		logger.info("Account CSV header: " + line);
		Map<String, Integer> columnIndex = getColumnIndices(line);

		while((line = reader.readLine()) != null){
			String[] elements = line.split(",");
            String accountID = elements[columnIndex.get("ACCOUNT_ID")];
			boolean isSAR = elements[columnIndex.get("IS_SAR")].toLowerCase().equals("true");
			float initBalance = Float.parseFloat(elements[columnIndex.get("INIT_BALANCE")]);
			String bankID = elements[columnIndex.get("BANK_ID")];


			Account account;
			if (isSAR) {
				account = new SARAccount(accountID, normalTxInterval, initBalance, bankID,
						getRandom());
			} else {
				account = new Account(accountID, normalTxInterval, initBalance, bankID,
						getRandom());
			}

			int index = this.getAccounts().size();
			account.setBranch(this.branches.get(index % this.numBranches));
			this.getAccounts().add(account);
			this.idMap.put(accountID, index);
			this.schedule.scheduleRepeating(account);
		}
		int numAccounts = idMap.size();
		logger.info("Number of total accounts: " + numAccounts);
		diameter = new Diameter(numAccounts);

		reader.close();
	}

	private void loadTransactionFile(String transactionFile) throws IOException{
		BufferedReader reader = new BufferedReader(new FileReader(transactionFile));
		String line = reader.readLine();
		Map<String, Integer> columnIndex = getColumnIndices(line);
		while((line = reader.readLine()) != null){
			String[] elements = line.split(",");
            String srcID = elements[columnIndex.get("src")];
            String dstID = elements[columnIndex.get("dst")];

            String ttype = elements[columnIndex.get("ttype")];

			Account src = getAccountFromID(srcID);
			Account dst = getAccountFromID(dstID);
			src.addBeneAcct(dst);
			src.addTxType(dst, ttype);
		}
		reader.close();
	}

	private void loadNormalModelsFile(String filename) throws IOException {
		try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
			String line;
			line = reader.readLine();
			Map<String, Integer> columnIndexMap = getColumnIndices(line);

			while((line = reader.readLine()) != null) {
				String[] elements = line.split(",");

				String type = elements[columnIndexMap.get("type")];
				String accountID = elements[columnIndexMap.get("accountID")];
				long accountGroupId = Long.parseLong(elements[columnIndexMap.get("modelID")]);
				boolean isMain = elements[columnIndexMap.get("isMain")].toLowerCase().equals("true");
				

				AccountGroup accountGroup;
				if (this.accountGroupsMap.containsKey(accountGroupId)) {
					accountGroup = this.accountGroupsMap.get(accountGroupId);
				}
				else {
					accountGroup = new AccountGroup(accountGroupId, this);
					accountGroupsMap.put(accountGroupId, accountGroup);
				}

				Account account = getAccountFromID(accountID);
				
				accountGroup.addMember(account);
				if (isMain) {
					accountGroup.setMainAccount(account);
				}

				account.addAccountGroup(accountGroup);

				AbstractTransactionModel model;

				switch (type) {
					case AbstractTransactionModel.SINGLE: model = new SingleTransactionModel(accountGroup, rand); break;
					case AbstractTransactionModel.FAN_OUT: model= new FanOutTransactionModel(accountGroup, rand); break;
					case AbstractTransactionModel.FAN_IN: model = new FanInTransactionModel(accountGroup, rand); break;
					case AbstractTransactionModel.MUTUAL: model = new MutualTransactionModel(accountGroup, rand); break;
					case AbstractTransactionModel.FORWARD: model = new ForwardTransactionModel(accountGroup, rand); break;
					case AbstractTransactionModel.PERIODICAL: model = new PeriodicalTransactionModel(accountGroup, rand); break;
					default: System.err.println("Unknown model type: " + type); model = new EmptyModel(accountGroup, rand); break;
				}

				accountGroup.setModel(model);

				model.setParameters(this.normalTxInterval, -1, -1);
			}
		}
	}


	private void loadAlertMemberFile(String alertFile) throws IOException{
		logger.info("Load alert member list from:" + alertFile);

		BufferedReader reader = new BufferedReader(new FileReader(alertFile));
		String line = reader.readLine();
		Map<String, Integer> columnIndex = getColumnIndices(line);
		Map<Long, Integer> scheduleModels = new HashMap<>();
		while((line = reader.readLine()) != null){
			String[] elements = line.split(",");
			long alertID = Long.parseLong(elements[columnIndex.get("alertID")]);
            String accountID = elements[columnIndex.get("accountID")];
			boolean isMain = elements[columnIndex.get("isMain")].toLowerCase().equals("true");
			boolean isSAR = elements[columnIndex.get("isSAR")].toLowerCase().equals("true");
			int modelID = Integer.parseInt(elements[columnIndex.get("modelID")]);
			double minAmount = Double.parseDouble(elements[columnIndex.get("minAmount")]);
		    double maxAmount = Double.parseDouble(elements[columnIndex.get("maxAmount")]);
			int startStep = Integer.parseInt(elements[columnIndex.get("startStep")]);
			int endStep = Integer.parseInt(elements[columnIndex.get("endStep")]);
			int scheduleID = Integer.parseInt(elements[columnIndex.get("scheduleID")]);

			if(minAmount > maxAmount){
				throw new IllegalArgumentException(String.format("minAmount %f is larger than maxAmount %f", minAmount, maxAmount));
			}
			if(startStep > endStep){
				throw new IllegalArgumentException(String.format("startStep %d is larger than endStep %d", startStep, endStep));
			}

			Alert alert;
			if(alerts.containsKey(alertID)){  // Get an AML typology object and update the minimum/maximum amount
				alert = alerts.get(alertID);
				AMLTypology model = alert.getModel();
				model.updateMinAmount(minAmount);
				model.updateMaxAmount(maxAmount);
				model.updateStartStep(startStep);
				model.updateEndStep(endStep);

			}else{  // Create a new AML typology object
				AMLTypology model = AMLTypology.createTypology(modelID, minAmount, maxAmount, startStep, endStep);
				alert = new Alert(alertID, model, this);
				alerts.put(alertID, alert);
			}
			Account account = getAccountFromID(accountID);
			alert.addMember(account);
			if(isMain){
				alert.setMainAccount(account);
			}
			account.setSAR(isSAR);
			scheduleModels.put(alertID, scheduleID);
		}
		for(long alertID : scheduleModels.keySet()){
			int modelID = scheduleModels.get(alertID);
			alerts.get(alertID).getModel().setParameters(modelID);
		}
		reader.close();
	}

	/**
	 * Define the simulator name and an output log directory.
	 * If the simulator name is not specified, generate it using the current time.
	 */
	private void initSimulatorName() {
		if(AMLSim.simulatorName == null) {  // Not specified in the args
			Calendar c = Calendar.getInstance();
			SimpleDateFormat format = new SimpleDateFormat("yyyyMMdd_HHmmss_SSS");
			AMLSim.simulatorName = "PS_" + format.format(c.getTime());
		}
		logger.info("Simulator Name: " + AMLSim.simulatorName);

        String dirPath = simProp.getOutputDir();
		File f = new File(dirPath);
		if(f.exists()){
            logger.warning("Output log directory already exists: " + dirPath);
        }else {
            boolean result = f.mkdir();
            if (!result) {
                throw new IllegalStateException("Output log directory cannot be created to: " + dirPath);
            }
        }
	}


	private void initTxLogBufWriter(String logFileName) {
		try {
			FileWriter writer = new FileWriter(new File(logFileName));
			this.bufWriter = new BufferedWriter(writer);
			this.bufWriter.write("step,type,amount,nameOrig,oldbalanceOrig,newbalanceOrig,nameDest,oldbalanceDest,newbalanceDest,isSAR,alertID\n");
			this.bufWriter.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	static String getTxLogFileName(){
		return txLogFileName;
	}

	public void executeSimulation(){
		//Load the parameters from the .property file
		loadParametersFromFile();

		// increase transfer limit with the current loop
		initSimulatorName();

		//Initiate the dumpfile output writer
        txLogFileName = simProp.getOutputTxLogFile();
		initTxLogBufWriter(txLogFileName);
		logger.info("Transaction log file: " + txLogFileName);

		// Create account objects
		super.start();
		this.initSimulation();

		// Starting the simulation
		long begin = System.currentTimeMillis();
		System.out.println("Starting AMLSim Running for " + numOfSteps + " steps. Current loop:" + AMLSim.currentLoop);

		long step;
		while ((step = super.schedule.getSteps()) < numOfSteps) {
			if (!super.schedule.step(this))
				break;
			if (step % 100 == 0 && step != 0) {
				long tm = System.currentTimeMillis();
				System.out.println("Time Step " + step + ", " + (tm - begin)/1000 + " [s]");
			}
			else {
				System.out.print("*");
			}
			if (computeDiameter && step % 10 == 0 && step > 0){
				double[] result = diameter.computeDiameter();
				writeDiameter(step, result);
			}
		}
		txs.flushLog();
		txs.writeCounterLog(numOfSteps, counterFile);
		System.out.println(" - Finished running " + step + " steps ");

		//Finishing the simulation
		super.finish();
		long end = System.currentTimeMillis();

		double total = end - begin;
		total = total/1000;  // ms --> s
		System.out.println("\nIt took: " + total + " seconds to execute the simulation\n");
		System.out.println("Simulation name: " + AMLSim.simulatorName);
	}
    
    /**
     * Manage a transaction for logging and diameter computation of the whole transaction network
     * ##param step Simulation step
     * ##param desc Transaction description (e.g. type)
     * ##param amt Amount
     * ##param orig Originator account
     * ##param bene Beneficiary account
     * ##param isSAR SAR flag
     * ##param alertID Alert ID
     */
	public static void handleTransaction(long step, String desc, double amt, Account orig, Account bene,
										 boolean isSAR, long alertID){
        // Reduce the balance of the originator account
        String origID = orig.getID();
		float origBefore = (float)orig.getBalance();
		orig.withdraw(amt);
		float origAfter = (float)orig.getBalance();
		
		// Increase the balance of the beneficiary account
        String beneID = bene.getID();
        float beneBefore = (float)bene.getBalance();
		bene.deposit(amt);
		float beneAfter = (float)bene.getBalance();

		txs.addTransaction(step, desc, amt, origID, beneID, origBefore, origAfter, beneBefore, beneAfter, isSAR, alertID);
		diameter.addEdge(origID, beneID);
	}
    
    /**
     * Write diameters of the transaction network snapshots to a CSV file
     * ##param step Simulation step
     * ##param result Diameter and radius of the transaction network as double array
     */
	private void writeDiameter(long step, double[] result){
		try{
			BufferedWriter writer = new BufferedWriter(new FileWriter(diameterFile, true));
			writer.write(step + "," + result[0] + "," + result[1] + "\n");
			writer.close();
		}catch (IOException e){
			e.printStackTrace();
		}
	}


	public static void main(String[] args){
        if(args.length < 1){
            System.err.println("Usage: java amlsim.AMLSim [ConfFile]");
            System.exit(1);
        }

		// Loading configuration JSON file instead of parsing command line arguments
        String confFile = args[0];
        try {
            simProp = new SimProperties(confFile);
        }catch (IOException e){
            System.err.println("Cannot load configuration JSON file: " + confFile);
            e.printStackTrace();
            System.exit(1);
        }

        if(args.length >= 2){  // Load transaction model parameter file (optional)
        	String propFile = args[1];
			ModelParameters.loadProperties(propFile);
		}

        int seed = simProp.getSeed();
        AMLSim sim = new AMLSim(seed);
        sim.setCurrentLoop(0);
        sim.executeSimulation();
	}
}

/* AMLSim/src/main/java/amlsim/Account.java */
package amlsim;

import amlsim.model.*;
import amlsim.model.cash.CashInModel;
import amlsim.model.cash.CashOutModel;
import sim.engine.SimState;
import sim.engine.Steppable;
import java.util.*;

public class Account implements Steppable {

    protected String id;

	protected CashInModel cashInModel;
	protected CashOutModel cashOutModel;
	protected boolean isSAR = false;
	private Branch branch = null;
	private Set<String> origAcctIDs = new HashSet<>();  // Originator account ID set
	private Set<String> beneAcctIDs = new HashSet<>();  // Beneficiary account ID set
    private List<Account> origAccts = new ArrayList<>();  // Originator accounts from which this account receives money
    private List<Account> beneAccts = new ArrayList<>();  // Beneficiary accounts to which this account sends money
	private int numSARBene = 0;  // Number of SAR beneficiary accounts
	private String bankID = "";  // Bank ID
    
    private Account prevOrig = null;  // Previous originator account

	List<Alert> alerts = new ArrayList<>();
	List<AccountGroup> accountGroups = new ArrayList<>();

    private Map<String, String> tx_types = new HashMap<>();  // Receiver Client ID --> Transaction Type

	private static List<String> all_tx_types = new ArrayList<>();

	private ArrayList<String> paramFile = new ArrayList<>();

	private double balance = 0;

	protected long startStep = 0;
	protected long endStep = 0;

	private Random random;


	public Account() {
		this.id = "-";
	}

	/**
	 * Constructor of the account object
	 * ##param id Account ID
     * ##param interval Default transaction interval
	 * ##param initBalance Initial account balance
	 * ##param start Start step
	 * ##param end End step
	 */
    public Account(String id, int interval, float initBalance, String bankID, Random rand) {
		this.id = id;
		this.setBalance(initBalance);
		this.bankID = bankID;
		this.random = rand;

		this.cashInModel = new CashInModel();
		this.cashInModel.setAccount(this);
		this.cashInModel.setParameters(interval, -1, -1);

		this.cashOutModel = new CashOutModel();
		this.cashOutModel.setAccount(this);
		this.cashOutModel.setParameters(interval, -1, -1);
	}

	public String getBankID() {
		return this.bankID;
	}

	public long getStartStep(){
		return this.startStep;
	}
	public long getEndStep(){
		return this.endStep;
	}

	void setSAR(boolean flag){
		this.isSAR = flag;
	}

	public boolean isSAR() {
		return this.isSAR;
	}

	public double getBalance() {
		return this.balance;
	}

	public void setBalance(double balance) {
		this.balance = balance;
	}

    public void withdraw(double ammount) {
        if (this.balance < ammount) {
            this.balance = 0;
        } else {
            this.balance -= ammount;
        }
    }

	public void deposit(double ammount){
		this.balance += ammount;
	}

	void setBranch(Branch branch) {
		this.branch = branch;
	}

	public Branch getBranch(){
		return this.branch;
	}

	public void addBeneAcct(Account bene){
		String beneID = bene.id;
		if(beneAcctIDs.contains(beneID)){  // Already added
			return;
		}

		if(ModelParameters.shouldAddEdge(this, bene)){
			beneAccts.add(bene);
			beneAcctIDs.add(beneID);

			bene.origAccts.add(this);
			bene.origAcctIDs.add(id);

			if(bene.isSAR){
				numSARBene++;
			}
		}
	}

	public void addTxType(Account bene, String ttype){
		this.tx_types.put(bene.id, ttype);
		all_tx_types.add(ttype);
	}

	public String getTxType(Account bene) {
		String destID = bene.id;

		if (this.tx_types.containsKey(destID)) {
			return tx_types.get(destID);
		} else if (!this.tx_types.isEmpty()) {
			List<String> values = new ArrayList<>(this.tx_types.values());
			return values.get(this.random.nextInt(values.size()));
		} else {
			return Account.all_tx_types.get(this.random.nextInt(Account.all_tx_types.size()));
		}
	}

	/**
	 * Get previous (originator) accounts
	 * ##return Originator account list
	 */
	public List<Account> getOrigList(){
		return this.origAccts;
	}

	/**
	 * Get next (beneficiary) accounts
	 * ##return Beneficiary account list
	 */
	public List<Account> getBeneList(){
		return this.beneAccts;
	}

	public void printBeneList(){
		System.out.println(this.beneAccts);
	}

	public int getNumSARBene(){
		return this.numSARBene;
	}

	public float getPropSARBene(){
		if(numSARBene == 0){
			return 0.0F;
		}
		return (float)numSARBene / beneAccts.size();
	}

	/**
	 * Register this account to the specified alert.
	 * ##param alert Alert
	 */
	public void addAlert(Alert alert) {
		this.alerts.add(alert);
	}
    

	public void addAccountGroup(AccountGroup accountGroup) {
		this.accountGroups.add(accountGroup);
	}

	/**
	 * Perform transactions
	 * ##param state AMLSim object
	 */
	##Override
	public void step(SimState state) {
		long currentStep = state.schedule.getSteps();  // Current simulation step
        long start = this.startStep >= 0 ? this.startStep : 0;
        long end = this.endStep > 0 ? this.endStep : AMLSim.getNumOfSteps();
		if(currentStep < start || end < currentStep){
			return;  // Skip transactions if this account is not active
		}
		handleAction(state);
	}


	public void handleAction(SimState state) {
		AMLSim amlsim = (AMLSim) state;
		long step = state.schedule.getSteps();
		
		for (Alert alert : this.alerts) {
			if (this == alert.getMainAccount()) {
				alert.registerTransactions(step, this);
			}
		}

		for (AccountGroup accountGroup : this.accountGroups) {
			Account account = accountGroup.getMainAccount();
			if (this == accountGroup.getMainAccount()) {
				accountGroup.registerTransactions(step, account);
			}
		}

		handleCashTransaction(amlsim);
	}

	/**
	 * Make cash transactions (deposit and withdrawal)
	 */
	private void handleCashTransaction(AMLSim amlsim){
		long step = amlsim.schedule.getSteps();
		this.cashInModel.makeTransaction(step);
		this.cashOutModel.makeTransaction(step);
	}

	/**
	 * Get the previous originator account
	 * ##return Previous originator account objects
	 */
	public Account getPrevOrig(){
		return prevOrig;
	}

	public String getName() {
        return this.id;
	}

	/**
	 * Get the account identifier as long
	 * ##return Account identifier
	 */
    public String getID(){
        return this.id;
    }

	/**
	 * Get the account identifier as String
	 * ##return Account identifier
	 */
	public String toString() {
		return "C" + this.id;
	}

	public ArrayList<String> getParamFile() {
		return paramFile;
	}

	public void setParamFile(ArrayList<String> paramFile) {
		this.paramFile = paramFile;
	}
}

/* AMLSim/src/main/java/amlsim/AccountGroup.java */

package amlsim;

import amlsim.model.AbstractTransactionModel;

import java.util.*;

public class AccountGroup {
    
    private long accountGroupId;
    private List<Account> members;  // Accounts involved in this alert
    private Account mainAccount;   // Main account of this alert
    private AbstractTransactionModel model;    // Transaction model
    private AMLSim amlsim;  // AMLSim main object

    AccountGroup(long accountGroupId, AMLSim sim) {
        this.accountGroupId = accountGroupId;
        this.members = new ArrayList<>();
        this.mainAccount = null;
        this.amlsim = sim;
    }


    void setModel(AbstractTransactionModel model) {
        this.model = model;
    }

    /**
     * Add transactions
     * 
     * ##param step Current simulation step
     */
    void registerTransactions(long step, Account acct) {
        // maybe add is valid step.
        model.sendTransactions(step, acct);
    }

    /**
     * Involve an account in this alert
     * 
     * ##param acct Account object
     */
    void addMember(Account acct) {
        this.members.add(acct);
    }

    /**
     * Get main AMLSim object
     * ##return AMLSim object
     */
    public AMLSim getSimulator(){
        return amlsim;
    }

    /**
     * Get account group identifier as long type
     * ##return Account group identifier
     */
    public long getAccoutGroupId(){
        return this.accountGroupId;
    }

    /**
     * Get member list of the alert
     * ##return Alert account list
     */
    public List<Account> getMembers(){
        return members;
    }

    /**
     * Get the main account
     * ##return The main account if exists.
     */
    public Account getMainAccount(){
        return mainAccount;
    }

    /**
     * Set the main account
     * ##param account Main account object
     */
    void setMainAccount(Account account){
        this.mainAccount = account;
    }

    public AbstractTransactionModel getModel() {
        return model;
    }

    public boolean isSAR() {
        return this.mainAccount != null && this.mainAccount.isSAR();
    }
}

/* AMLSim/src/main/java/amlsim/Alert.java */

package amlsim;

import amlsim.model.aml.AMLTypology;

import java.util.*;

/*
 * Group of suspicious transactions and involving accounts as an AML typology
 * Accounts in this class perform suspicious transactions based on the given typology (model)
 */
public class Alert {

    private long alertID;  // Alert identifier
    private List<Account> members;  // Accounts involved in this alert
    private Account mainAccount;   // Main account of this alert
    private AMLTypology model;    // Transaction model
    private AMLSim amlsim;  // AMLSim main object

    Alert(long alertID, AMLTypology model, AMLSim sim){
        this.alertID = alertID;
        this.members = new ArrayList<>();
        this.mainAccount = null;
        this.model = model;
        this.model.setAlert(this);
        this.amlsim = sim;
    }

    /**
     * Add transactions
     * ##param step Current simulation step
     */
    void registerTransactions(long step, Account acct){
        if(model.isValidStep(step)){
            model.sendTransactions(step, acct);
        }
    }

    /**
     * Involve an account in this alert
     * ##param acct Account object
     */
    void addMember(Account acct){
        this.members.add(acct);
        acct.addAlert(this);
    }

    /**
     * Get main AMLSim object
     * ##return AMLSim object
     */
    public AMLSim getSimulator(){
        return amlsim;
    }

    /**
     * Get alert identifier as long type
     * ##return Alert identifier
     */
    public long getAlertID(){
        return alertID;
    }

    /**
     * Get member list of the alert
     * ##return Alert account list
     */
    public List<Account> getMembers(){
        return members;
    }

    /**
     * Get the main account
     * ##return The main account if exists.
     */
    public Account getMainAccount(){
        return mainAccount;
    }

    /**
     * Set the main account
     * ##param account Main account object
     */
    void setMainAccount(Account account){
        this.mainAccount = account;
    }

    public AMLTypology getModel(){
        return model;
    }

    public boolean isSAR(){
        return this.mainAccount != null && this.mainAccount.isSAR();
    }
}

/* AMLSim/src/main/java/amlsim/Branch.java */

package amlsim;

/**
 * A branch of a bank
 * In cash transactions, this class perform like an account
 */
public class Branch extends Account {

    private int id;  // Branch identifier
    private float limitAmount = 100.0F;  // Limit of deposit/withdrawal amount

    public Branch(int id){
        this.id = id;
    }

    /**
     * Get the limit of deposit/withdrawal amount
     * ##return Limit of deposit/withdrawal amount
     */
    public float getLimitAmount(){
        return limitAmount;
    }

    /**
     * Get the branch identifier as String
     * ##return Branch identifier
     */
    public String toString(){
        return "B" + this.id;
    }

    /**
     * Get the branch identifier as String
     * ##return Branch identifier
     */
    public String getName() {
        return toString();
    }

}


/* AMLSim/src/main/java/amlsim/SARAccount.java */
package amlsim;

import java.util.Random;

import amlsim.model.aml.*;
import sim.engine.SimState;

/**
 * Suspicious account class
 */
public class SARAccount extends Account {

	private int count = 0;

	SARAccount(String id, int interval, float init_balance, String bankID, Random random) {
		super(id, interval, init_balance, bankID, random);
		this.isSAR = true;
	}

	public void handleAction(SimState state){
	    AMLSim amlsim = (AMLSim) state;
		super.handleAction(amlsim);

		boolean success = handleAlert(amlsim);
		if(success){
			count++;
		}
	}

	private boolean handleAlert(AMLSim amlsim){
		if(alerts.isEmpty()){
			return false;
		}

		Alert fg = alerts.get(count % alerts.size());
		AMLTypology model = fg.getModel();

		// this is no-op
		// figure out why this was here.
		model.makeTransaction(amlsim.schedule.getSteps());
		return true;
	}

	public String toString() {
		return "F" + this.id;
	}

}

/* AMLSim/src/main/java/amlsim/TargetedTransactionAmount.java */

package amlsim;
import java.util.Random;

public class TargetedTransactionAmount {

   private SimProperties simProperties;
   private Random random;
   private double target;

   public TargetedTransactionAmount(Number target, Random random) {
       this.simProperties = AMLSim.getSimProp();
       this.random = random;
       this.target = target.doubleValue();
   }

   public double doubleValue() {
       double minTransactionAmount = simProperties.getMinTransactionAmount();
       double maxTransactionAmount = simProperties.getMaxTransactionAmount();
       double min, max, result;
       
       if (this.target < maxTransactionAmount) {
           max = this.target;
       }
       else {
           max = maxTransactionAmount;
       }
       
       if (this.target < minTransactionAmount) {
           min = this.target;
       }
       else {
           min = minTransactionAmount;
       }
 
       if (max - min <= 0)
       {
           result = this.target;
       }
       if (this.target - min <= 100)
       {
           result = this.target;
       }
       else
       {
           result =  min + random.nextDouble() * (max - min);
       }
       return result;
    }
}

/* AMLSim/src/main/java/amlsim/TransactionRepository.java */

package amlsim;

import java.io.BufferedWriter;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.*;

/**
 * AML Transaction repository (set of transactions) for performance optimizations
 */
public class TransactionRepository {

    public final int size;
    private int index = 0;
//    private DecimalFormat amt_fmt;

    private int count = 0;
    private int limit = Integer.MAX_VALUE;  // Number of transactions as buffer

    private long[] steps;
    private String[] descriptions;
    private double[] amounts;
    private String[] origIDs;
    private String[] destIDs;

    private float[] origBefore;
    private float[] origAfter;
    private float[] destBefore;
    private float[] destAfter;
    private boolean[] isSAR;
    private long[] alertIDs;

    private Map<Long, Integer> txCounter;
    private Map<Long, Integer> sarTxCounter;

    TransactionRepository(int size) {
        this.txCounter = new HashMap<>();
        this.sarTxCounter = new HashMap<>();

        this.size = size;
        this.steps = new long[size];
        this.descriptions = new String[size];
        this.amounts = new double[size];
        this.origIDs = new String[size];
        this.destIDs = new String[size];

        this.origBefore = new float[size];
        this.origAfter = new float[size];
        this.destBefore = new float[size];
        this.destAfter = new float[size];
        this.isSAR = new boolean[size];
        this.alertIDs = new long[size];
    }

    void setLimit(int limit){
        this.limit = limit;
    }

    void addTransaction(long step, String desc, double amt, String origID, String destID, float origBefore,
                        float origAfter, float destBefore, float destAfter, boolean isSAR, long aid){
        if(count >= limit){
            if(count == limit){
                System.err.println("Warning: the number of output transactions has reached the limit: " + limit);
                flushLog();
                count++;
            }
            return;
        }

        this.steps[index] = step;
        this.descriptions[index] = desc;
        this.amounts[index] = amt;
        this.origIDs[index] = origID;
        this.destIDs[index] = destID;
        this.origBefore[index] = origBefore;
        this.origAfter[index] = origAfter;
        this.destBefore[index] = destBefore;
        this.destAfter[index] = destAfter;
        this.isSAR[index] = isSAR;
        this.alertIDs[index] = aid;

        if(isSAR){
            sarTxCounter.put(step, sarTxCounter.getOrDefault(step, 0) + 1);
        }else if(!desc.contains("CASH-")) {
            txCounter.put(step, txCounter.getOrDefault(step, 0) + 1);  // Exclude cash transactions for counter
            count--;
        }

        count++;
        index++;
        if(index >= size){
            flushLog();
        }
    }

    private double getDoublePrecision(double d) {
        // Round down amount to two digits (e.g. 12.3456 --> 12.34)
        // DecimalFormat will not be used because of its computation cost
        return (int)(d * 100) / 100.0;
    }

    void writeCounterLog(long steps, String logFile){
        try {
            BufferedWriter writer = new BufferedWriter(new FileWriter(logFile));
            writer.write("step,normal,SAR\n");
            for(long i=0; i<steps; i++){
                int numTx = txCounter.getOrDefault(i, 0);
                int numSARTx = sarTxCounter.getOrDefault(i, 0);
                writer.write(i + "," + numTx + "," + numSARTx + "\n");
            }
            writer.flush();
        }catch(IOException e){
            e.printStackTrace();
        }
    }

    void flushLog(){
        // Flush transaction logs to the CSV file
        try {
            FileWriter writer1 = new FileWriter(new File(AMLSim.getTxLogFileName()), true);
            BufferedWriter writer = new BufferedWriter(writer1);

            for(int i = 0; i < this.index; i++){
                writer.write(steps[i] + "," + descriptions[i] + "," + getDoublePrecision(amounts[i]) + "," +
                        origIDs[i] + "," + getDoublePrecision(origBefore[i]) + "," + getDoublePrecision(origAfter[i]) + "," +
                        destIDs[i] + "," + getDoublePrecision(destBefore[i]) + "," + getDoublePrecision(destAfter[i]) + "," +
                        (isSAR[i] ? "1" : "0") + "," + alertIDs[i] + "\n");
            }
            writer.flush();
            writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        index = 0;
    }

}

/* AMLSim/src/main/java/amlsim/model/aml/AMLTypology.java */

// Source: http://files.acams.org/pdfs/English_Study_Guide/Chapter_5.pdf
// Suspicious transactions which may lead to money laundering
// - Unusually high monthly balances in comparison to known sources of income.
// - Unusually large deposits, deposits in round numbers or deposits in repeated amounts that are not attributable to legitimate sources of income.
// - Multiple deposits made under reportable thresholds.
// - The timing of deposits. This is particularly important when dates of illegal payments are known.
// - Checks written for unusually large amounts (in relation to the suspect's known practices).
// - A lack of account activity. This might indicate transactions in currency or the existence of other unknown bank accounts.
//
// Note: No specific bank models are used for this AML typology model class.

package amlsim.model.aml;

import amlsim.AMLSim;
import amlsim.Account;
import amlsim.Alert;
import amlsim.model.AbstractTransactionModel;

/**
 * Suspicious transaction models
 */
public abstract class AMLTypology extends AbstractTransactionModel {

    // Transaction model ID of AML typologies
    private static final int AML_FAN_OUT = 1;
    private static final int AML_FAN_IN = 2;
    private static final int CYCLE = 3;
    private static final int BIPARTITE = 4;
    private static final int STACK = 5;
    private static final int RANDOM = 6;
    private static final int SCATTER_GATHER = 7;  // fan-out -> fan-in
    private static final int GATHER_SCATTER = 8;  // fan-in -> fan-out

    // Transaction scheduling ID
    static final int FIXED_INTERVAL = 0;  // All accounts send money in order with the same interval
    static final int RANDOM_INTERVAL = 1;  // All accounts send money in order with random intervals
    static final int UNORDERED = 2;  // All accounts send money randomly
    static final int SIMULTANEOUS = 3;  // All transactions are performed at single step simultaneously

    final double marginRatio = AMLSim.getSimProp().getMarginRatio();  // Each member holds this ratio of the received amount
    
    /**
     * Create an AML typology object (alert transaction model)
     * ##param modelID Alert transaction model ID as int
     * ##param minAmount Minimum transaction amount
     * ##param maxAmount Maximum transaction amount
     * ##param startStep Start simulation step (all transactions will start after this step)
     * ##param endStep End simulation step (all transactions will finish before this step)
     * ##return AML typology model object
     */
    public static AMLTypology createTypology(int modelID, double minAmount, double maxAmount,
                                             int startStep, int endStep) {
        AMLTypology model;
        switch(modelID){
            case AML_FAN_OUT: model = new FanOutTypology(minAmount, maxAmount, startStep, endStep); break;
            case AML_FAN_IN: model = new FanInTypology(minAmount, maxAmount, startStep, endStep); break;
            case CYCLE: model = new CycleTypology(minAmount, maxAmount, startStep, endStep); break;
            case BIPARTITE: model = new BipartiteTypology(minAmount, maxAmount, startStep, endStep); break;
            case STACK: model = new StackTypology(minAmount, maxAmount, startStep, endStep); break;
            case RANDOM: model = new RandomTypology(minAmount, maxAmount, startStep, endStep); break;
            case SCATTER_GATHER: model = new ScatterGatherTypology(minAmount, maxAmount, startStep, endStep); break;
            case GATHER_SCATTER: model = new GatherScatterTypology(minAmount, maxAmount, startStep, endStep); break;
            default: throw new IllegalArgumentException("Unknown typology model ID: " + modelID);
        }
        model.setParameters(startStep, endStep);
        return model;
    }

    Alert alert;
    protected double minAmount;
    protected double maxAmount;
    protected long startStep;
    protected long endStep;

    /**
     * Set parameters (timestamps and amounts) of transactions
     * ##param modelID Scheduling model ID
     */
    public abstract void setParameters(int modelID);

//    /**
//     * Get the number of total transactions in this alert
//     * ##return Number of transactions
//     */
//    public abstract int getNumTransactions();
    
    /**
     * Bind this alert transaction model to the alert
     * ##param ag Alert object
     */
    public void setAlert(Alert ag){
        this.alert = ag;
    }

    /**
     * Whether the current simulation step is within the valid simulation step range
     * ##param step Current simulation step
     * ##return If the current step is within the valid simulation step range, return true
     */
    public boolean isValidStep(long step){
        return startStep <= step && step <= endStep;
    }


    public int getStepRange(){
        return (int)(endStep - startStep + 1);
    }

    /**
     * Construct an AML typology with the given basic parameters
     * ##param minAmount Minimum transaction amount (each transaction amount must not be lower than this value)
     * ##param maxAmount Maximum transaction amount (each transaction amount must not be higher than this value)
     * ##param startStep Start simulation step of alert transactions (any transactions cannot be carried out before this step)
     * ##param endStep End simulation step of alert transactions (any transactions cannot be carried out after this step)
     */
    public AMLTypology(double minAmount, double maxAmount, int startStep, int endStep){
        this.minAmount = minAmount;
        this.maxAmount = maxAmount;
        this.startStep = startStep;
        this.endStep = endStep;
    }

    /**
     * Update the minimum transaction amount if the given amount is smaller than the current one
     * ##param minAmount New minimum amount
     */
    public void updateMinAmount(double minAmount) {
        this.minAmount = Math.min(this.minAmount, minAmount);
    }

    /**
     * Update the maximum transaction amount if the given amount is larger than the current one
     * ##param maxAmount New maximum amount
     */
    public void updateMaxAmount(double maxAmount) {
        this.maxAmount = Math.max(this.maxAmount, maxAmount);
    }

    public void updateStartStep(long startStep){
        this.startStep = Math.min(this.startStep, startStep);
    }

    public void updateEndStep(long endStep){
        this.endStep = Math.max(this.endStep, endStep);
    }

    /**
     * Generate a random amount
     * ##return A random amount within "minAmount" and "maxAmount"
     */
    double getRandomAmount(){
        return alert.getSimulator().random.nextDouble() * (maxAmount - minAmount) + minAmount;
    }

    /**
     * Generate a random simulation step
     * ##return Random simulation step within startStep and endStep
     */
    long getRandomStep(){
        return alert.getSimulator().random.nextLong(getStepRange()) + startStep;
    }

    /**
     * Generate a random simulation step from the given start and end steps
     * ##param start Minimum simulation step
     * ##param end Maximum simulation
     * ##return Random simulation step within the given step range
     */
    long getRandomStepRange(long start, long end){
        if(start < startStep || endStep < end){
            throw new IllegalArgumentException("The start and end steps must be within " + startStep + " and " + endStep);
        }else if(end < start){
            throw new IllegalArgumentException("The start and end steps are unordered");
        }
        long range = end - start;
        return alert.getSimulator().random.nextLong(range) + start;
    }


    ##Override
    public String getModelName() {
        return "AMLTypology";
    }

    // ???
    // this is a no-op coming from SAR Account
    // this eventually should go away.
    // To Do Jordan D. Nelson
    public final void makeTransaction(long step) {
    }
}
/* AMLSim/src/main/java/amlsim/model/aml/BipartiteTypology.java */

//
// Note: No specific bank models are used for this AML typology model class.
//

package amlsim.model.aml;

import amlsim.AMLSim;
import amlsim.Account;
import amlsim.TargetedTransactionAmount;

import java.util.*;

/**
 * Bipartite transaction model
 * Some accounts send money to a different account set
 */
public class BipartiteTypology extends AMLTypology {

    private Random random = AMLSim.getRandom();

    ##Override
    public void setParameters(int modelID) {

    }
    
    public BipartiteTypology(double minAmount, double maxAmount, int minStep, int maxStep) {
        super(minAmount, maxAmount, minStep, maxStep);
    }

    ##Override
    public String getModelName() {
        return "BipartiteTypology";
    }

    ##Override
    public void sendTransactions(long step, Account acct) {
        List<Account> members = alert.getMembers();  // All members

        int last_orig_index = members.size() / 2;  // The first half accounts are originators
        for (int i = 0; i < last_orig_index; i++) {
            Account orig = members.get(i);
            if (!orig.getID().equals(acct.getID())) {
                continue;
            }

            TargetedTransactionAmount transactionAmount = getTransactionAmount(members.size() - last_orig_index,
                    orig.getBalance());

            for (int j = last_orig_index; j < members.size(); j++) {
                Account bene = members.get(j); // The latter half accounts are beneficiaries
                makeTransaction(step, transactionAmount.doubleValue(), orig, bene);
            }
        }
    }


    private TargetedTransactionAmount getTransactionAmount(int numBene, double origBalance) {
        if (numBene == 0) {
            return new TargetedTransactionAmount(0, random);
        }
        return new TargetedTransactionAmount(origBalance / numBene, random);
    }
}

/* AMLSim/src/main/java/amlsim/model/cash/CashInModel.java */
package amlsim.model.cash;

import amlsim.Branch;

/**
 * Cash-in (deposit) model
 */
public class CashInModel extends CashModel {

    private static int NORMAL_INTERVAL = 1;
    private static int SUSPICIOUS_INTERVAL = 1;
    private static float NORMAL_MIN = 10;
    private static float NORMAL_MAX = 100;
    private static float SUSPICIOUS_MIN = 10;
    private static float SUSPICIOUS_MAX = 100;

    public static void setParam(int norm_int, int case_int, float norm_min, float norm_max, float case_min, float case_max){
        NORMAL_INTERVAL = norm_int;
        SUSPICIOUS_INTERVAL = case_int;
        NORMAL_MIN = norm_min;
        NORMAL_MAX = norm_max;
        SUSPICIOUS_MIN = case_min;
        SUSPICIOUS_MAX = case_max;
        System.out.println("Norm: " + NORMAL_INTERVAL + " Case: " + SUSPICIOUS_INTERVAL);
    }

    private boolean isNextStep(long step){
        return false;
    }

    private float computeAmount(){
        if(this.account.isSAR()){
            return SUSPICIOUS_MIN + rand.nextFloat() * (SUSPICIOUS_MAX - SUSPICIOUS_MIN);
        }else{
            return NORMAL_MIN + rand.nextFloat() * (NORMAL_MAX - NORMAL_MIN);
        }
    }

    ##Override
    public String getModelName() {
        return "CASH-IN";
    }

    ##Override
    public void makeTransaction(long step) {
        if(isNextStep(step)){
            Branch branch = account.getBranch();
            float amount = computeAmount();
            makeTransaction(step, amount, account, branch, "CASH-IN");
        }
    }
}

/* AMLSim/src/main/java/amlsim/model/cash/CashModel.java */

package amlsim.model.cash;

import amlsim.AMLSim;
import amlsim.Account;
import amlsim.model.AbstractTransactionModel;

import java.util.Random;

/**
 * Cash transaction model (between an account and a deposit account)
 * There are two subclasses: CashInModel (deposit) and CashOutModel (withdrawal)
 */
public abstract class CashModel extends AbstractTransactionModel {

    protected static Random rand = AMLSim.getRandom();

    // needed for cash accounts temporarily
    protected Account account;

    protected double[] randValues;  // Random values to generate transaction amounts
    protected static final int randSize = 10;  // Number of random values to be stored

    public void setAccount(Account account) {
        this.account = account;
    }

    public CashModel(){
        randValues = new double[randSize];
        for(int i = 0; i< randSize; i++){
            randValues[i] = rand.nextGaussian();  // from -1.0 to 1.0
        }
    }

    // to satisfy interface
    public void sendTransactions(long step, Account acct) {

    }

    // Abstract methods from TransactionModel
    public abstract String getModelName();  // Get transaction type description
    public abstract void makeTransaction(long step);  // Create and add transactions
}

/* AMLSim/src/main/java/amlsim/model/cash/CashOutModel.java */
package amlsim.model.cash;

import amlsim.Branch;

/**
 * Cash-out (withdrawal) model
 */
public class CashOutModel extends CashModel {

    private static int NORMAL_INTERVAL = 1;
    private static int SUSPICIOUS_INTERVAL = 1;
    private static float NORMAL_MIN = 10;
    private static float NORMAL_MAX = 100;
    private static float SUSPICIOUS_MIN = 10;
    private static float SUSPICIOUS_MAX = 100;

    public static void setParam(int norm_int, int case_int, float norm_min, float norm_max, float case_min, float case_max){
        NORMAL_INTERVAL = norm_int;
        SUSPICIOUS_INTERVAL = case_int;
        NORMAL_MIN = norm_min;
        NORMAL_MAX = norm_max;
        SUSPICIOUS_MIN = case_min;
        SUSPICIOUS_MAX = case_max;
    }

    private boolean isNextStep(long step){
        return false;
    }

    private float computeAmount(){
        if(this.account.isSAR()){
            return SUSPICIOUS_MIN + rand.nextFloat() * (SUSPICIOUS_MAX - SUSPICIOUS_MIN);
        }else{
            return NORMAL_MIN + rand.nextFloat() * (NORMAL_MAX - NORMAL_MIN);
        }
    }

    ##Override
    public String getModelName() {
        return "CASH-OUT";
    }

    ##Override
    public void makeTransaction(long step) {
//        List<AMLTransaction> txs = new ArrayList<>();
        if(isNextStep(step)){
            Branch branch = account.getBranch();
            float amount = computeAmount();
            makeTransaction(step, amount, branch, account, "CASH-OUT");
        }
    }
}

/* AMLSim/src/main/java/amlsim/model/AbstractTransactionModel.java */
package amlsim.model;

import amlsim.Account;
import amlsim.AccountGroup;
import amlsim.AMLSim;

/**
 * Base class of transaction models
 */
public abstract class AbstractTransactionModel {

    // Transaction model ID
    public static final String SINGLE = "single";  // Make a single transaction to each neighbor account
    public static final String FAN_OUT = "fan_out";  // Send money to all neighbor accounts
    public static final String FAN_IN = "fan_in";  // Receive money from neighbor accounts
    public static final String MUTUAL = "mutual";
    public static final String FORWARD = "forward";
    public static final String PERIODICAL = "periodical";

//    protected static Random rand = new Random(AMLSim.getSeed());

    protected AccountGroup accountGroup;  // Account group object
    protected int interval = 1; // Default transaction interval
    protected long startStep = -1;  // The first step of transactions
    protected long endStep = -1;  // The end step of transactions
    protected boolean isSAR = false;

    /**
     * Get the assumed number of transactions in this simulation
     * ##return Number of total transactions
     */
    public int getNumberOfTransactions(){
        return (int)AMLSim.getNumOfSteps() / interval;
    }
    
    /**
     * Set an account object group which has this model
     * 
     * ##param accountGroup account group object
     */
    public void setAccountGroup(AccountGroup accountGroup) {
        this.accountGroup = accountGroup;
    }

    /**
     * Get the simulation step range as the period when this model is valid
     * If "startStep" and/or "endStep" is undefined (negative), it returns the largest range
     * ##return The total number of simulation steps
     */
    public int getStepRange(){
        long st = startStep >= 0 ? startStep : 0;
        long ed = endStep > 0 ? endStep : AMLSim.getNumOfSteps();
        return (int)(ed - st + 1);
    }

    /**
     * Get transaction model name
     * ##return Transaction model name
     */
    public abstract String getModelName();

    
    /**
     * Generate the start transaction step (to decentralize transaction distribution)
     * ##param range Simulation step range
     * ##return random int value [0, range-1]
     */
    protected static int generateStartStep(int range){
//        return rand.nextInt(range);
        return AMLSim.getRandom().nextInt(range);
    }

    /**
     * Set initial parameters
     * This method will be called when the account is initialized
     * ##param interval Transaction interval
     * ##param start Start simulation step (It never makes any transactions before this step)
     * ##param end End simulation step (It never makes any transactions after this step)
     */
    public void setParameters(int interval, long start, long end){
        this.interval = interval;
        setParameters(start, end);
    }
    
    /**
     * Set initial parameters of the transaction model (for AML typology models)
     * ##param start Start simulation step
     * ##param end End simulation step
     */
    public void setParameters(long start, long end){
        this.startStep = start;
        this.endStep = end;
    }

    /**
     * The new workhorse method.
     * ##param step
     * ##param account
     */
    public abstract void sendTransactions(long step, Account account);

    /**
     * Generate and register a transaction (for alert transactions)
     * ##param step Current simulation step
     * ##param amount Transaction amount
     * ##param orig Origin account
     * ##param dest Destination account
     * ##param isSAR Whether this transaction is SAR
     * ##param alertID Alert ID
     */
    protected void makeTransaction(long step, double amount, Account orig, Account dest, boolean isSAR, long alertID){
        if(amount <= 0){  // Invalid transaction amount
           // AMLSim.getLogger().warning("Warning: invalid transaction amount: " + amount);
            return;
        }
        String ttype = orig.getTxType(dest);
        if(isSAR) {
            AMLSim.getLogger().fine("Handle transaction: " + orig.getID() + " -> " + dest.getID());
        }
        AMLSim.handleTransaction(step, ttype, amount, orig, dest, isSAR, alertID);
    }

    /**
     * Generate and register a transaction (for cash transactions)
     * ##param step Current simulation step
     * ##param amount Transaction amount
     * ##param orig Origin account
     * ##param dest Destination account
     * ##param ttype Transaction type
     */
    protected void makeTransaction(long step, float amount, Account orig, Account dest, String ttype){
        AMLSim.handleTransaction(step, ttype, amount, orig, dest, false, -1);
    }

    /**
     * Generate and register a transaction (for normal transactions)
     * ##param step Current simulation step
     * ##param amount Transaction amount
     * ##param orig Origin account
     * ##param dest Destination account
     */
    protected void makeTransaction(long step, double amount, Account orig, Account dest){
        makeTransaction(step, amount, orig, dest, false, -1);
    }
}
/* AMLSim/src/main/java/amlsim/model/AbstractTransactionModel.java */

package amlsim.model;

import java.io.*;
import java.util.Random;
import java.util.Properties;

import amlsim.AMLSim;
import amlsim.Account;

/**
 * Adjust transaction parameters for fine-tuning of the transaction network
 */
public class ModelParameters {

//    private static Random rand = new Random(AMLSim.getSeed());
    private static Random rand = AMLSim.getRandom();
    private static Properties prop = null;

    private static float SAR2SAR_EDGE_THRESHOLD = 0.0F;
    private static float SAR2NORMAL_EDGE_THRESHOLD = 0.0F;
    private static float NORMAL2SAR_EDGE_THRESHOLD = 0.0F;
    private static float NORMAL2NORMAL_EDGE_THRESHOLD = 0.0F;
    
    private static float SAR2SAR_TX_PROB = 1.0F;
    private static float SAR2NORMAL_TX_PROB = 1.0F;
    private static float NORMAL2SAR_TX_PROB = 1.0F;
    private static float NORMAL2NORMAL_TX_PROB = 1.0F;

    private static float SAR2SAR_AMOUNT_RATIO = 1.0F;
    private static float SAR2NORMAL_AMOUNT_RATIO = 1.0F;
    private static float NORMAL2SAR_AMOUNT_RATIO = 1.0F;
    private static float NORMAL2NORMAL_AMOUNT_RATIO = 1.0F;

    private static float NORMAL_HIGH_RATIO = 1.0F;  // High transaction amount ratio from normal accounts
    private static float NORMAL_LOW_RATIO = 1.0F;  // Low transaction amount ratio from normal accounts
    private static float NORMAL_HIGH_PROB = 0.0F;  // Probability of transactions with high amount
    private static float NORMAL_LOW_PROB = 0.0F;  // Probability of transactions with low amount
    private static float NORMAL_SKIP_PROB = 0.0F;  // Probability of skipping transactions

    /**
     * Whether it adjusts parameters of normal transactions
     * ##return If true, it affects to normal transaction models
     * If false, it does not any effects to all normal transactions
     */
    public static boolean isValid(){
        return prop != null;
    }
    
    private static float getRatio(String key, float defaultValue){
        String value = System.getProperty(key);
        if(value == null){
            value = prop.getProperty(key, String.valueOf(defaultValue));
        }
        return Float.parseFloat(value);
    }

    private static float getRatio(String key){
        return getRatio(key, 1.0F);
    }

    public static void loadProperties(String propFile){
        if(propFile == null){
            return;
        }
        System.out.println("Model parameter file: " + propFile);
        try{
            prop = new Properties();
            prop.load(new FileInputStream(propFile));
        }catch (IOException e){
            System.err.println("Cannot load model parameter file: " + propFile);
            e.printStackTrace();
            prop = null;
            return;
        }

        SAR2SAR_EDGE_THRESHOLD = getRatio("sar2sar.edge.threshold");
        SAR2NORMAL_EDGE_THRESHOLD = getRatio("sar2normal.edge.threshold");
        NORMAL2SAR_EDGE_THRESHOLD = getRatio("normal2sar.edge.threshold");
        NORMAL2NORMAL_EDGE_THRESHOLD = getRatio("normal2normal.edge.threshold");
        
        SAR2SAR_TX_PROB = getRatio("sar2sar.tx.prob");
        SAR2NORMAL_TX_PROB = getRatio("sar2normal.tx.prob");
        NORMAL2SAR_TX_PROB = getRatio("normal2sar.tx.prob");
        NORMAL2NORMAL_TX_PROB = getRatio("normal2normal.tx.prob");

        SAR2SAR_AMOUNT_RATIO = getRatio("sar2sar.amount.ratio");
        SAR2NORMAL_AMOUNT_RATIO = getRatio("sar2normal.amount.ratio");
        NORMAL2SAR_AMOUNT_RATIO = getRatio("normal2sar.amount.ratio");
        NORMAL2NORMAL_AMOUNT_RATIO = getRatio("normal2normal.amount.ratio");

        NORMAL_HIGH_RATIO = getRatio("normal.high.ratio", 1.0F);
        NORMAL_LOW_RATIO = getRatio("normal.low.ratio", 1.0F);
        NORMAL_HIGH_PROB = getRatio("normal.high.prob", 1.0F);
        NORMAL_LOW_PROB = getRatio("normal.low.prob", 1.0F);
        NORMAL_SKIP_PROB = getRatio("normal.skip.prob", 1.0F);
        if(NORMAL_HIGH_RATIO < 1.0){
            throw new IllegalArgumentException("The high transaction amount ratio must be 1.0 or more");
        }
        if(NORMAL_LOW_RATIO <= 0.0 || 1.0 < NORMAL_LOW_RATIO){
            throw new IllegalArgumentException("The low transaction amount ratio must be positive and 1.0 or less");
        }
        if(1.0 < NORMAL_HIGH_PROB + NORMAL_LOW_PROB + NORMAL_SKIP_PROB){
            throw new IllegalArgumentException("The sum of high, low and skip transaction probabilities" +
                                                       " must be 1.0 or less");
        }

        System.out.println("Transaction Probability:");
        System.out.println("\tSAR -> SAR: " + SAR2SAR_TX_PROB);
        System.out.println("\tSAR -> Normal: " + SAR2NORMAL_TX_PROB);
        System.out.println("\tNormal -> SAR: " + NORMAL2SAR_TX_PROB);
        System.out.println("\tNormal -> Normal: " + NORMAL2NORMAL_TX_PROB);

        System.out.println("Transaction edge addition threshold (proportion of SAR accounts):");
        System.out.println("\tSAR -> SAR: " + SAR2SAR_EDGE_THRESHOLD);
        System.out.println("\tSAR -> Normal: " + SAR2NORMAL_EDGE_THRESHOLD);
        System.out.println("\tNormal -> SAR: " + NORMAL2SAR_EDGE_THRESHOLD);
        System.out.println("\tNormal -> Normal: " + NORMAL2NORMAL_EDGE_THRESHOLD);

        System.out.println("Transaction amount ratio:");
        System.out.println("\tSAR -> SAR: " + SAR2SAR_AMOUNT_RATIO);
        System.out.println("\tSAR -> Normal: " + SAR2NORMAL_AMOUNT_RATIO);
        System.out.println("\tNormal -> SAR: " + NORMAL2SAR_AMOUNT_RATIO);
        System.out.println("\tNormal -> Normal: " + NORMAL2NORMAL_AMOUNT_RATIO);
    }

    /**
     * Generate an up to 10% ratio noise for the base transaction amount
     * ##return Amount ratio [0.9, 1.1]
     */
    public static float generateAmountRatio(){  // [0.9, 1.1]
        return rand.nextFloat() * 0.2F + 0.9F;
    }

    /**
     * Adjust transaction amount from the given accounts and the base amount
     * ##param orig Originator account
     * ##param bene Beneficiary account
     * ##param baseAmount Base amount
     * ##return Adjusted amount (If it should not make this transaction, return non-positive value)
     */
    public static float adjustAmount(Account orig, Account bene, float baseAmount){
        // Generate decentralized amount with up to 10% noise
        float amount = baseAmount * generateAmountRatio();

        if(!isValid()){
            return amount;
        }

        float ratio;
        float prob = rand.nextFloat();
        
        if(orig.isSAR()){  // SAR originator
            if(bene.isSAR()){  // SAR -> SAR
                if(SAR2SAR_TX_PROB <= prob){
                    return 0.0F;
                }
                ratio = SAR2SAR_AMOUNT_RATIO;
            }else{  // SAR -> Normal
                if(SAR2NORMAL_TX_PROB <= prob){
                    return 0.0F;
                }
                ratio = SAR2NORMAL_AMOUNT_RATIO;
            }
        }else{  // Normal originator
            if(bene.isSAR()){  // Normal -> SAR
                if(NORMAL2SAR_TX_PROB <= prob){
                    return 0.0F;
                }
                ratio = NORMAL2SAR_AMOUNT_RATIO;
            }else{  // Normal -> Normal
                if(NORMAL2NORMAL_TX_PROB <= prob){
                    return 0.0F;
                }
                ratio = NORMAL2NORMAL_AMOUNT_RATIO;
            }
            
            prob = rand.nextFloat();
            if(prob < NORMAL_HIGH_PROB){  // High-amount payment transaction (near to the upper limit)
                ratio *= NORMAL_HIGH_RATIO;
            }else if(prob < NORMAL_HIGH_PROB + NORMAL_LOW_PROB){  // Low-amount transaction
                ratio *= NORMAL_LOW_RATIO;
            }else if(prob < NORMAL_HIGH_PROB + NORMAL_LOW_PROB + NORMAL_SKIP_PROB){
                return 0.0F;  // Skip this transaction
            }
        }
        return amount * ratio;
    }

    /**
     * Determine whether the transaction edge should be actually added between the given accounts
     * ##param orig Originator account
     * ##param bene Beneficiary account
     * ##return If the transaction should be actually added, return true.
     */
    public static boolean shouldAddEdge(Account orig, Account bene){
        if(!isValid()){  // It always adds this edge
            return true;
        }
        // Proportion of SAR beneficiary accounts of the originator account
//        float benePropThreshold = SAR2NORMAL_EDGE_THRESHOLD;
//        int beneNumThreshold = (int) Math.floor(1 / NORMAL2SAR_EDGE_THRESHOLD);

        int numNeighbors = orig.getBeneList().size();
        float propSARBene = orig.getPropSARBene();

        if(orig.isSAR()){  // SAR originator
            if(bene.isSAR()){  // SAR -> SAR
                return propSARBene >= SAR2SAR_EDGE_THRESHOLD;
            }else{  // SAR -> Normal
                // Allow edge creations if the ratio of SAR beneficiary accounts is enough large
                return propSARBene >= SAR2NORMAL_EDGE_THRESHOLD;
            }
        }else{  // Normal originator
            if(bene.isSAR()){  // Normal -> SAR
                // Create a transaction edge if the ratio of SAR beneficiary accounts is still large
                if(NORMAL2SAR_EDGE_THRESHOLD <= 0.0F){
                    return true;
                }
                return numNeighbors > (int) Math.floor(1 / NORMAL2SAR_EDGE_THRESHOLD)
                        && propSARBene >= NORMAL2SAR_EDGE_THRESHOLD;
            }else{  // Normal -> Normal
                return propSARBene >= NORMAL2NORMAL_EDGE_THRESHOLD;
            }
        }
    }

}

/* AMLSim/src/main/java/amlsim/stat/Diameter.java */

package amlsim.stat;

import java.io.*;
import java.util.*;
import it.unimi.dsi.webgraph.*;
import it.unimi.dsi.webgraph.algo.HyperBall;

/**
 * Compute diameter and average distance of the transaction graph
 */
public class Diameter {

    private ArrayListMutableGraph graph;  // Transaction graph (WebGraph)
    private Map<String, Integer> id2idx;  // Account ID --> Index of Graph

    private Map<Integer, Set<Integer>>  adj;  // Adjacency set (account index --> neighbor account index)

    public Diameter(int numAccounts){
        this.graph = new ArrayListMutableGraph(numAccounts);
        this.id2idx = new HashMap<>(numAccounts);
        this.adj = new HashMap<>(numAccounts);
    }

    /**
     * Add an edge to the internal transaction graph
     * ##param srcID source account ID
     * ##param dstID destination account ID
     */
//    public void addEdge(long srcID, long dstID){
    public void addEdge(String srcID, String dstID){
        if(!id2idx.containsKey(srcID)){
            int idx = id2idx.size();
            id2idx.put(srcID, idx);
            adj.put(idx, new HashSet<>());
        }
        if(!id2idx.containsKey(dstID)){
            int idx = id2idx.size();
            id2idx.put(dstID, idx);
            adj.put(idx, new HashSet<>());
        }
        int srcIdx = id2idx.get(srcID);
        int dstIdx = id2idx.get(dstID);
        if(!adj.get(srcIdx).contains(dstIdx)) {  // If this edge is not yet added, add it
            graph.addArc(srcIdx, dstIdx);
            adj.get(srcIdx).add(dstIdx);
        }
    }

    /**
     * Compute diameter and average length with HyperANF
     * ##return Diameter and average length as an array of double values
     */
    public double[] computeDiameter(){
        int log2m = 10;  // Register length exponent: it affects elapsed time, memory consumption and precision)
        int th = 4;  // Number of threads
        int maxDistance = 50;  // Limit of distance
        double aver = 0.0;
        double[] result = new double[2];

        try {
            ImmutableGraph g = graph.immutableView();
            HyperBall hyperanf = new HyperBall(g, null, log2m, null, th, 0, 0, false);
            hyperanf.init();
            int connectedNodes = g.numNodes();

            long start = System.currentTimeMillis();
            int prev = connectedNodes;
            for(int i=0; i<maxDistance; i++){
                hyperanf.iterate();
                int mod = hyperanf.modified();
                int num = prev - mod;

                if(i == 0){
                    connectedNodes -= num;
                }else{
                    aver += i * (double)num / connectedNodes;
                }

//                System.out.println("Step:" + i + " Average Distance:" + aver);
                prev = mod;
                if(mod == 0){  // reached all vertices
                    System.out.println("Diameter: " + i);
                    result[0] = i;
                    break;
                }
            }
            System.out.println("Average Distance: " + aver);
            result[1] = aver;

            hyperanf.close();
            long end = System.currentTimeMillis();
            System.out.println("Elapsed Time: " + (end - start)/1000 + "s");
            return result;

        } catch (IOException e) {
            System.err.println("Cannot load and compute graph data");
            e.printStackTrace();
            return null;
        }
    }

    public static void main(String[] args) throws IOException{
        int num_accts = Integer.parseInt(args[0]);
        String tx_csv = args[1];

        List<List<Integer>> srcs = new ArrayList<>();
        List<List<Integer>> dsts = new ArrayList<>();
        int num_bins = 16;
        for(int i=0; i<num_bins; i++){
            srcs.add(new ArrayList<>());
            dsts.add(new ArrayList<>());
        }

        System.out.println("Load Transaction List");
        String line;
        String[] data;
        BufferedReader br = new BufferedReader(new FileReader(tx_csv));
        br.readLine();
        while((line = br.readLine()) != null){
            data = line.split(",");
            int src = Integer.parseInt(data[1]);
            int dst = Integer.parseInt(data[2]);
            int tm = Integer.parseInt(data[6]);

            int bin = tm / 10;
            srcs.get(bin).add(src);
            dsts.get(bin).add(dst);
        }

        System.out.println("Compute Diameter");
        Diameter diameter = new Diameter(num_accts);
        for(int i=0; i<num_bins; i++){
            int tm = i * 10;
            List<Integer> src_list = srcs.get(i);
            List<Integer> dst_list = dsts.get(i);

            int num_edges = src_list.size();
            for(int j=0; j<num_edges; j++){
                String src = String.valueOf(src_list.get(j));
                String dst = String.valueOf(dst_list.get(j));
                diameter.addEdge(src, dst);
            }

            double[] ret = diameter.computeDiameter();
            System.out.println(tm + "," + ret[0] + "," + ret[1]);
        }
    }

}