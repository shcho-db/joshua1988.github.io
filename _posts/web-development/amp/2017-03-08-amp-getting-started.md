---
layout: article
title: "최신 웹 트렌드 AMP 시작하기"
date: 2017-03-08 14:36:32 +0900
categories: [web-development, amp]
excerpt: "AMP 소개, 동작원리, 입문용 5분 튜토리얼"
image:
  teaser: posts/web/amp/amp.png
  credit: Brian Jackson
  creditlink: https://kinsta.com/blog/google-amp/
  #url to their site or licensing
locale: "ko_KR"
# 리플 옵션
comments: true
tags:
- AMP
- Accelarted Mobile Page
- web developement
- 웹 최신 트렌드
- 웹 개발
- Google AMP
- AMP 소개
- AMP 튜토리얼
---
{% include toc.html %}

## AMP (Accelarated Mobile Page)
- 모바일 기기에서 웹 사이트의 접근성을 높이기 위한 가속화 모바일 페이지
- 거의 즉시에 가까운 페이지 로딩을 위해 여러 기법들로 최적화 하였고 AMP HTML / JS / CSS 로 구성되어 있다.

## AMP 동작 원리
- AMP 에 포함되는 모든 자바스크립트는 비동기 방식으로 실행된다.

  - AMP 에는 개발자가 직접 작성한 별도의 자바스크립트를 포함할 수 없다.
  - 페이지 동작과 관련된 부분은 커스텀 AMP 요소를 통해 구현할 수 있다.
  - iframe 안에 별도로 외부 자바스크립트 라이브러리를 포함할 수 있다. (메인 페이지 렌더링에 방해 안됨)

- 이미지, 광고, iframe 과 같은 외부 리소스들의 사이즈와 위치를 지정해야 한다.

  - 외부 리소스를 다운받기 전에 HTML 요소의 사이즈를 지정하면,
    리소스 다운로드 여부와 관계 없이 페이지 레이아웃을 정할 수 있다.
  - 이러한 원리로 전체 문서의 레이아웃을 정하기 위해 단 한번의 HTTP 요청만 필요하다.
  - 따라서, 리소스가 로딩될 때 레이아웃을 다시 그릴 필요가 없다.

- 커스텀 스크립트를 사용하는 경우 커스텀 태그를 지정해주어야 한다.


```html
<script async custom-element="amp-iframe" src="https://cdn.ampproject.org/v0/amp-youtube-0.1.js"></script>
```


- 외부 라이브러리들이 렌더링 동작에 방해되지 않도록 한다.

  - AMP 는 외부 자바스크립트 라이브러리를 iframes 안에서만 허용한다. (메인 페이지 실행을 방해하지 않음)
  - iframes 에서 스타일 재계산이나 페이지 레이아웃 재조정이 일어나더라도, DOM 사이즈가 작기 때문에 속도가 빠르다.

- 모든 CSS 스타일은 인라인이어야 하고 사이즈가 제한되어 있다.

  - 인라인 CSS 스타일은 1 개만 허용되기 때문에 다른 웹 사이트에 비해 최소 1 개 이상의 HTTP 요청을 줄인다.
  - 인라인 CSS 스타일의 최대 사이즈는 50kb

- 웹 폰트 요청은 효율적으로 해야 한다.

  - 일반적인 웹 페이지에서는 외부 자바스크립트와 스타일 시트를 순차적으로 로딩한 후 폰트를 다운받는다.
  - AMP 에서는 폰트를 받기까지 외부 자바스크립트와 스타일 시트를 다운받는 HTTP 요청이 존재하지 않음
  - 자바스크립트의 경우 async 속성을, 스타일 시트의 경우 인라인을 이용하기 때문

- 스타일 재계산 최소화하기

  - 페이지에서 요소 측정이 일어날 때마다 스타일이 재 계산된다.
  - 그리고 이는 전체 페이지 레이아웃 재조정과 연결되기 때문에 많은 비용이 소모된다.
  - 모든 DOM 을 화면에 그리기 전에 읽기 때문에 한 프레임당 최대 한번만 스타일을 재계산한다.

- GPU 가속화 애니메이션만 사용

  - 빠른 성능을 위한 최적화 방법은 GPU 위에서 애니메이션을 실행하는 것이다.
  - GPU 의 단점은 페이지 레이아웃 재조정을 못하는 것인데 이 작업을 보통 브라우저에 위임한다.
  - 애니메이션은 GPU 에서 실행될 수 있도록 CSS 규칙을 정해야 한다.
  - 특히, AMP 는 애니메이션 작업과 변환 작업을 transform 과 opacity 에서 하기 때문에 레이아웃 조정이 필요없다.

- AMP 는 리소스 로딩 순서를 조정한다.

  - AMP 는 리소스 다운로드를 모두 제어하고 중요도에 따라 순서를 정한다.
  - 이미지나 광고는 필요한 경우 (스크롤 해서 보여지는 경우) 에만 재빨리 다운로드 한다.
  - 레이지 로딩 관련 리소스는 pre-fetch 를 해놓기 때문에, 로딩이 되어야 하는 시점에 더 빠르게 로딩되고 CPU 는 필요시에만 사용된다.

- 페이지 즉시 로딩하기

  - DNS Lookup, TCP Handshake 등을 미리 처리하는 [preconnect API](https://www.w3.org/TR/resource-hints/#dfn-preconnect) 로 HTTP 요청을 최대한 빨리 한다.
  - prerending 의 경우 모든 웹 컨텐츠에 적용되며 많은 양의 대역폭과 CPU 를 소비한다.
  - AMP 는 위의 단점들을 보완하여 최적화하고, CPU 가 많이 소비되는 rendering 의 경우에는 prerendering 을 하지 않는다.

## AMP 시작하기
- AMP 의 기본적인 코드 구조는 다음과 같다.

```html
<!doctype html>
<html amp lang="en">
  <head>
    <meta charset="utf-8">
    <script async src="https://cdn.ampproject.org/v0.js"></script>
    <title>Hello, AMPs</title>
    <link rel="canonical" href="http://example.ampproject.org/article-metadata.html" />
    <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">
    <script type="application/ld+json">
      {
        "@context": "http://schema.org",
        "@type": "NewsArticle",
        "headline": "Open-source framework for publishing content",
        "datePublished": "2015-10-07T12:02:41Z",
        "image": [
          "logo.jpg"
        ]
      }
    </script>
    <style amp-boilerplate>body{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}</style><noscript><style amp-boilerplate>body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}</style></noscript>
  </head>
  <body>
    <h1>Welcome to the mobile web</h1>
  </body>
</html>
```

- 위 문서에서 AMP 와 관련된 부분은

```html
<!-- AMP 자바스크립트 라이브러리 로딩 -->
<script async src="https://cdn.ampproject.org/v0.js"></script>

<!-- AMP CSS 스타일 -->
<style amp-boilerplate>body{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}</style><noscript><style amp-boilerplate>body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}</style></noscript>
```

## AMP 이미지 추가
- AMP 에서 이미지 추가는 아래의 태그를 사용한다.

``` html
<amp-img src="welcome.jpg" alt="Welcome" height="400" width="800"></amp-img>
```

- AMP 에서 일반 HTML 태그들은 그대로 사용되지만, 위와 같이 img 태그는 amp-img 로 변환하여 사용한다.
- embed, param 등등 몇 개의 HTML 표준 태그들은 AMP 에서 사용할 수 없다. [참고](https://github.com/ampproject/amphtml/blob/master/spec/amp-html-format.md)
- amp-img 태그를 사용하는 이유는 해당 리소스가 로딩되기 전에 페이지 레이아웃을 정하고 레이지 로딩에 대한 네트워크 제어와 리소스 로딩 우선순위를 효율적으로 관리하기 위함.


## AMP CSS 스타일링 추가
- AMP 의 요소에 대한 스타일링은 `<style amp-custom>` 태그를 이용한다.

```html
<style amp-custom>
  /* any custom style goes here */
  body {
    background-color: white;
  }
  amp-img {
    background-color: gray;
    border: 1px solid black;
  }
</style>
```

- AMP CSS 스타일링에 대해 주의할 점은 다음과 같다.

  - 모든 AMP 페이지는 `<style amp-custom>` 태그 한 개만 포함할 수 있다.
  - HTML 인라인 CSS 를 사용할 수 없다. 모든 스타일 규칙은 head 안에 선언되어야 함.
  - `!important` 를 비롯하여 몇몇 표준 스타일 규칙을 사용할 수 없고 외부 스타일 시트 참조도 불가능하다. (커스텀 폰트 제외)

## AMP 미리보기와 유효성 검사
- 일반 HTML 문서와 동일하게 브라우저에서 파일을 보거나 간단한 웹서버로 미리 보기가 가능하다.
- 해당 문서의 AMP 규칙 준수 여부는 URL 끝에 `#development=1` 추가하여 개발자 도구 콘솔에서 유효성 검사로 알 수 있다.

## AMP 검색과 배포
- 같은 웹 콘텐츠에 대해 AMP, non AMP 페이지 2 개를 모두 갖고 있는 경우가 있다.
- 이러한 경우 `<link>` 를 이용해서 두 페이지를 연결한다.
- 일반 HTML 페이지에는

```html
<link rel="amphtml" href="https://www.example.com/url/to/amp/document.html">
```

- AMP 페이지에는

```html
<link rel="canonical" href="https://www.example.com/url/to/full/document.html">
```

- 만약 구분없이 AMP 페이지만 가지고 있을 경우

```html
<link rel="canonical" href="https://www.example.com/url/to/amp/document.html">
```

## 튜토리얼 결과 페이지
- 위 코드들의 결과물은 아래와 같다.

![위의 튜토리얼 코드를 붙여 만든 간단한 AMP]({{ site.url }}/images/posts/web/amp/getting-started-result.png)

## 참고
- [AMP 공식 사이트](https://www.ampproject.org/)
- [Google 공식 블로그](https://googleblog.blogspot.kr/2015/10/introducing-accelerated-mobile-pages.html)
