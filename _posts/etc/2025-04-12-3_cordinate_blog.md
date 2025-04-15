---
title: "[Blog] 3. 블로그 공사하기"
categories: [ETC, Blog]
tags: [jekyll, github]
date: 2025-04-12 14:30:00 +0900
comments: false
excerpt: "크고 작은 디자인 수정에 대한 기록"
---

---

- [x] 본문 너비 늘리기
- [x] 테마색 파란거 갈색으로 변경
- [x] 코드 길어지면 자동 개행
- [x] 링크드인 없애기
- [x] title 누르면 About으로 가기
- [x] Disqus 없애기
- [x] 사이드바 구성 바꾸기
- [x] Tag 본문 위로 올리기
- [ ] 사이드바 젤 밑에 여백 넣기
- [ ] Home: Tag 크기 키우고, Top-Catg별로 자동 사진 넣기
- [ ] post글 젤 밑에 같은 카테고리의 글 리스트 5개 단위로 보여주는거 필요
- [ ] Top에 있는 카테고리 잘 안잡히는거 수정 + 위치 조정
- [ ] TOC 손보기

> jekyll Doc 참고: <https://jekyllrb.com/docs/posts/>

---

## 본문 너비 늘리기
```css
/* _sass/addon/variables.scss */
	$main-content-max-width: 1300px !default; // 기존 1150px
	$panel-max-width: 250px !default; // 기존 300px

/* _sass/addon/commons.scss */
@media all and (min-width: 1400px) {
  #main > div.row {
    padding-left: calc((100% - #{$main-content-max-width}) / 2);
    > div.col-xl-8 {
      max-width: 1050px; // 기존 850px
    }
  }
}

/* 사이드바도 50씩 줄이기 */
$sidebar-width: 210px !default;        /* the basic width : 260px */ 
$sidebar-width-small: 160px !default;  /* screen width: >= 850px, <= 1199px (iPad landscape): 210px */
$sidebar-width-large: 300px !default;  /* screen width: >= 1650px: 350px */

```

## 테마색 파란거 갈색으로 변경
```css
/* _sass\colors\light-typography.scss */
  --link-color: #ccb494; // 기존 파랑 2a408e

/* _sass/layout/post.scss */
/* 앞뒤 포스트 이동 */
.post-navigation {
  padding-top: 3rem;
  padding-bottom: 4rem;

  .btn {
    @include btn-post-nav;

    color: var(--link-color);

    &:hover {
      background: #ccb494; // 기존 파랑 2a408e
      color: #fff;
      border-color: #ccb494; // 기존 파랑 2a408e
    }
  }
}

/* 태그 색 */
#panel-wrapper {
  /* the headings */
  .panel-heading {
    @include label(inherit);
  }

  .post-tag {
    display: inline-block;
    line-height: 1rem;
    font-size: 0.85rem;
    background: none;
    border: 1px solid var(--btn-border-color);
    border-radius: 0.8rem;
    padding: 0.3rem 0.5rem;
    margin: 0 0.35rem 0.5rem 0;

    &:hover {
      background-color: #ccb494; // 기존 파랑 2a408e
      border-color: #ccb494; // 기존 파랑 2a408e
      color: #fff;
      transition: none;
    }
  }
}
```

## 코드 길어지면 자동 개행
```css
/* _sass\addon\commons.scss */

/* Code block auto-wrap */
pre {
  white-space: pre-wrap !important;
  word-break: break-word;
  overflow-wrap: break-word;
}
```

## 링크드인 없애기
```liquid
{% raw %}
<!-- _includes\sidebar.html -->
    {% for entry in site.data.contact %}
      {% capture url %}
        {%- if entry.type == 'github' -%}
          https://github.com/{{ site.github.username }}
        {%- elsif entry.type == 'email' -%}
          {% assign email = site.social.email | split: '@' %}
          javascript:location.href = 'mailto:' + ['{{ email[0] }}','{{ email[1] }}'].join('@')
        {%- elsif entry.type == 'linkedin' -%}   // 여기를 그냥 지웠음
          https://www.linkedin.com/in/{{ site.linkedin.usercode }}  // liquid 주석처리는 raw 태그를 써야 함
        {%- else -%}
          {{ entry.url }}
        {%- endif -%}
      {% endcapture %}
{% endraw %}
<!-- _data\contact.yml 여기랑 -->
<!-- _data\share.yml 여기도 -->
```

## title 누르면 About으로 가기
```html
<!-- _includes\sidebar.html -->
    <div class="site-title mt-3">
      <a href="{{ '/' | relative_url }}">{{ site.title }}</a>
    </div>
<!-- 저 링크를 /about/으로 변경 -->

```

## 사이드바 구성 바꾸기 (TODO)
    - 다 없애고 Category 내용을 저기다가 보여주고 싶음
    - 근데 카테고리에서 밑으로 내려가는거 왜 안됨
    - 엥 tab 2번 하면 Plaintext 코드 블록으로 보여주나?

## post에서 title-content 사이 개조
```html
{% raw %}
<!-- 이것들 다 삭제하고 tag 위로 올려줌 (backup)-->

<!-- lastmod date -->
{% if page.last_modified_at %}
    <span>
      {{ site.data.locales[lang].post.updated }}
      {% include datetime.html date=page.last_modified_at tooltip=true %}
    </span>
    {% endif %}

<!-- author -->
<span>
      {% capture author_name %}{{ site.data.authors[page.author].name | default: site.social.name }}{% endcapture %}
      {% assign author_link = nil %}

      {% if page.author %}
        {% assign author_link = site.data.authors[page.author].url %}
      {% elsif author_name == site.social.name %}
        {% assign author_link = site.social.links[0] %}
      {% endif %}

      {{ site.data.locales[lang].post.written_by }}

      <em>
        {% if author_link %}
          <a href="{{ author_link }}">{{ author_name }}</a>
        {% else %}
        {{ author_name }}
        {% endif %}
      </em>
    </span>

<!-- read time -->
      {% include read-time.html content=content prompt=true %}
{% endraw %}
```


## Tag 및 Post-mata 정보 크기 키우기
```css
/* _sass/addon/commons.scss */
.post-tags {
  line-height: 1.5rem;
  font-size: 1.2rem; // add
}

.post-meta {
  font-size: 1.1rem; // 0.85rem
}
```

## TOC 왜 2,3수준밖에 안보이지?
- 그리고 3수준도 계속 보여줬음 하는데

