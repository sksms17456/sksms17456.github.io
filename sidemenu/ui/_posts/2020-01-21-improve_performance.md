---
layout: post
title: Front-End 성능에 대한 이해
description: >
    [프론트엔드 성능에 대해 이해해보자~]
excerpt_separator: <!--more-->

---

<!--more-->
# 사용자 중심적 성능 지표


## 1. User Key Moments

1. FP/First Contentful Paint
   - **is it happening?**
   - 페이지에 '무엇'인가가 출력되고, 네비게이션이 진행되는 것을 보여주는 단계

2. First Meaningful Paint
   - **is it useful?**
   - '크리티컬' 컨텐츠가 페인팅된 단계

3. Time to Interactive
   - **is it usable?, is it delightful?**
   - 비주얼적으로 '완료'되고, 인터렉션이 가능한 단계



## 2. Hero Element

- 페이지 내 컨텐츠들 중, 주요한 의미를 갖는 컨텐츠 요소
- 이 요소들이 얼마나 빨리 디스플레이 되냐에 따라서 사용자들이 체감적으로 느끼는 속도감이 다르다.


<br>
<br>
<br>

# Front-End 성능 개선 방법

## 1. Request 수 줄이기

##### 여러 개의 분리된 파일을 병합해 로딩되도록 하면 병합된 파일의 수 만큼 HTTP Request 수를 줄임으로써 초기 로딩속도를 개선할 수 있다.

### 1-1. Domain Sahrding

- Domain당 병렬적으로 연결할 수 있는 **connection** 수가 정해져 있다.
- 브라우저는 도메인 별 한번에 처리할 수 있는 요청의 개수가 제한되어 있어, 페이지 내 리소스가 단일 도메인에서만 요청되는 경우 병목현상이 발생할 수 있다. 
  - 이 경우, 여러 개의 다른 도메인에서 리소스를 다운로드 받도록 처리하면 병목현상을 제거할 수 있다.

> **주의!!**
>
> 추가적인 도메인의 사용은 DNS lookup 비용이 발생한다.
>
> 2~4개 도메인 내외를 사용 권장(많은 sharding은 오히려 성능에 악영향을 줄 수 있다.)



### 1-2. 자원의 Merge

- 여러 개의 스크립트를 별도의 request로 loading하지 않고, **하나로 병합하여 하나의 request로 로딩**하면, 각각의 request마다 발생되는 프로세스 단계들을 여러 번 거치는게 아니라 단순화하여 하나의 단계로 처리할 수 있다.
- Merge한 컨텐츠의 크기가 너무 커지게 되면 컨텐츠 다운로드 시간도 길어지기 때문에 적절히 Merge해주는 것이 좋다.



### 1-3. DataURI Scheme

> 작은 용량의 파일을 문서에 인라인으로 삽입할 수 있도록 함

~~~html
일반적 형태
<img src="http://www.naver.com/naver.png"/>

Data URI
<img src="data:image/png;base64,iVBOR...rkjggg==" />
~~~

- src에 이미지 데이터 문자열로 삽입을 하게되면 자체가 데이터기 때문에 외부데이터 요청이 일어나지 않아 request요청이 일어나지 않는다.
- 이미지 자체는 **binary data**인데, 브라우저에서 이를 인식할 수 있는 **base64**로 encoding해 문서에 삽입되 출력되도록 할 수 있다.

> **주의!!**
>
> DataURI는 별도의 파일이 아니므로 caching 되지 않는다.	
>
> 이미지 파일을 base64로 encoding 후 output 하는 작업이 별도로 필요하다.
>
> 브라우저에 따라 DataURI 지원 최대 크기가 다르다.

#### 언제 적용해야 할까?

1. 검색 결과를 나타내는 페이지의 경우, 시간이 흐름에 따라 검색 결과도 달라지기 때문에 굳이 요소가 캐싱할 필요가 없는 데이터
2. 배경 이미지로 크기가 CSS Sprite이 적합하지 않은 경우
   - 너비/높이가 큰 경우, sprite 사용시 여백이 많이 생길 수 있어 단독 이미지 형태로 사용해야 하는 경우가 발생할 수 있다.



### 1-4. Lazy loading

- 현재 필요 없는 자원을 onload 이후나, 화면에 노출되는 시점에 자원을 불러들이는 방법으로, 초기 로딩속도를 개선할 수 있다.
- 당장 사용자가 사용하지 않을 것으로 기대되는 데이터와 보이지 않는 영역의 데이터는 사용자의 액션시점 이후 또느 해당 요소가 보이기 직전에 로딩한다.



### 1-5. Code-Splitting

- 당장 페이지 로딩 시점에 사용되지 않는 코드들을 별도의 chunk파일로 분리하면 초기 로딩이 되는 request의 용량이 줄어든다.


<br>


## 2. 리소스 크기 줄이기

> 전송되는 **HTTP Request 양을 줄임**으로써 초기 로딩속도를 개선할 수 있다.

### 2-1. Minify

- 공백제거, 주석제거를 하여 크기를 줄일 수 있다.



### 2-2. Obfuscation

- 변수명을 변경하여 크기를 줄일 수 있다.



### 2-3. 이미지 최적화의 필요성

> 평균적으로 웹 페이지는 43%가 이미지로 구성되어 있다.

1. 적절한 이미지 포맷의 사용
   - PNG : png-8, png-24에 따라 용량 차이가 남.
   - JPEG : 압축률 70~90%에 따라, png보다 용량이 작아짐

    | Graphic Format | Size (KB) | Loss of Colour Information |
    | :------------: | :-------: | :------------------------: |
    |      SVG       |   10.2    |             No             |
    |      GIF       |   14.9    |            Yes             |
    |      PNG       |   30.8    |             No             |
    |      JPEG      |   48.9    |             No             |
    |      TIFF      |   66.2    |             No             |
    | Monochrome BMP |    109    |            Yes             |
    | 16-Colour BMP  |    434    |            Yes             |
    | 2560Colour BMP |    870    |            Yes             |

2. 기기에 대응되는 적절한 크기의 이미지 사용

   >  이미지가 브라우저에 출력되기 위해선 decode과정을 거쳐야 한다.

   - Encode : 그래픽 프로그램 등을 통해 RGB의 값을 이미지 데이터로 변환하는 과정
   - Decode : 이미지 데이터를 RGB로 변환하는 과정
   - 출력 과정 : Request -> Decode -> Copy to GPU -> Display
   - 고해상도 디스플레이 대응을 위해 큰 이미지를 작은 사이즈로 지정해 출력하는 경우 decoding 비용이 원래 크기로 출력하는 것보다 많은 비용이 발생한다.
   - 기기의 pixel ratio에 맞는 이미지를 사용해 decode 비용과 불필요한 네트워크 비용을 줄일 수 있다.

   ~~~html
   <img src="image2.jpg" srcset="image1.jpg 1x, image.jpg 2x, image.jpg 3x" width="160" height="107">
   ~~~

3. 역할에 비해 과도하게 큰 이미지 사용 X

   - 동일한 색상의 단순 배경처리에 큰 이미지를 사용하는 경우, 네트워크 및 리소스 처리 등에 불필요한 비용이 발생된다.

4. 이미지 metadata

   - 실제 이미지 표현과 관련 없는 메타 정보가 존재함.
   - 표현에 불필요한 메타 정보를 제거

5. 이미지 최적화 도구 등을 사용해 용량 감소

6. 포토인프라를 사용해 이미지 delivery를 최적화

   - Thumbnail 생성과 이미지 quality 등을 필요에 따라 delivery 받을 수 있다.
   - 불필요한 meta 정보 제거
   - 이미지 포맷 변환 (원본 이미지 포맷과 다른 출력 포맷)
   - 이미지 압축(JPG경우 - 기본 90%)



### 2-4. HTTP 헤더에서 불필요한 값 제거

- 모든 request에는 cookie가 같이 전달되어 network 오버헤드가 발생한다.
- 정적인 파일인 경우, cookie-free 도메인을 이용하는 것이 좋다.
- **pstatic.net** 을 이용하여 불필요한 cookie 전송을 HTTP헤더에서 제거할 수 있다.



### 2-5. Tree-Shaking: Dead Code Elimination

> ES6 모듈을 사용하는 경우, 사용되는 것만 import 해야한다.

##### Webpack에서 tree-shaking 적용

1. ES6 모듈 syntax 사용 (i.e. import와 export).
2. package.json에 "sideEffects" 속성 설정
3. mode:"production" 설정


<br>


## 3. 빠르게 렌더링 하기

1. 브라우저 렌더링 과정 (Reflow)

   - 페이지 레이아웃에 따른 각 요소의 포매팅 처리(위치, 순서, 배치, 간격 등)을 의미한다.

2. 자원의 위치에 따른 렌더링 특징

   - 스크립트는 다양한 작업을 수행하는 코드로 이루어져 있고, DOM을 변경하는 작업들이 포함되어 있을 수 있기 때문에, HTML 파싱 작업이 블럭된다.

3. script 태그의 Defer 와 Async 속성

    - **defer**
      - script 태그를 다운로드 하는 동안에도 HTML 파싱을 중단하지 않고, HTML 파싱이 완료된 이후 스크립트를 실행
      - DOM 제어와 관련이 있는 스크립트는 **defer를** 이용
    - **async**
      - script 태그를 다운로드 하는 동안에도 HTML 파싱을 중단하지 않고, 다운로드가 완료되면 바로 스크립트를 실행. 이 때 HTML 파싱이 중단
      - 구글 analytics 와 같이 DOM 제어와 관련이 없고, 의존성이 없는 스크립트는 **async** 를 이용
      - 단, Async=false일 경우 (기본값은 true), 스크립트 삽입 순서를 보장한다.

4. Reflow의 최소화

   - 동적으로 요소를 숨기거나 추가, 삭제 또는 브라우저 크기 변경 등으로 인해 포매팅이 재조정되는 요인이 발생하면 새로운 레이아웃에 맞는 포매티이 처리 작작업인 **Reflow**가 발생한다.

   - DOM Tree에서 분리 후 작업, 이후 최종 반영한다.

    ~~~javascript
    //분리 전. 두 번의 reflow가 발생할 여지가 있음
    //실제로는 브라우저가 내부적으로 최적화를 통해 reflow를 줄인다.
    var obj = document.createElement("div");
    document.body.appendChild(obj);
    obj.style.width = "300px";
    obj.style.height = "100px";
    
    //분리 후. reflow를 한 번으로 줄여놓을 수 있다.
    var obj = document.createElement("div");
    obj.style.width = "300px";
    obj.style.height = "100px";
    document.body.appendChild(obj);
    ~~~

   - Rendor Tree에서 분리 후 작업, 이후 최종 반영한다.

    ~~~javascript
    var el = document.getElementById("container");
    el.style.display = "none";
    
    for(var i=0; i<1000; i++) {
      el.style.left += value+i;
    }
    el.style.display = "block";
    ~~~

   - JavaScript 객체에 반영 후, 한꺼번에 반영한다.

    ~~~javascript
    var tmpObj = obj.cloneNode(true);
    tmpObj.style.width = "1000px";
    tmpObj.style.height = "300px";
    obj.parentNode.replaceChild(tmpObj, obj);
    ~~~

  - **CSS Containment - Limit Rendering Scope**

     - 스타일, 레이아웃 및 페인트 등, reflow가 발생될 수 있는 작업들을 특정 요소의 범위에 한정해 수행되도록 스코브를 제한하는 CSS 속성


<br>


## 4. 페이지 용량 감소

> 인라인 코드를 분리해 페이지 크기를 감소시켜라

- 인라인 JS/CSS로 기술된 코드들은 로컬 캐싱되지 않으므로, 가능한 경우라면 별도의 파일로 처리하는 것이 좋다.
- 인라인으로 포함된 코드는 매번 서버에서 다운로드 된다.
- 별도 분리된 파일으느 최초 다운로드 이후, 로컬 캐싱되어 다운로드 되지 않는다.


<br>


## 5. 여러 개의 분리된 script 블럭

> 여러 개의 script 블럭은 병합해 처리하라

- 여러 개의 **script tag**는 DOM node를 생성하고, 렌더링 되지 않지만 별도의 노드로 처리하기 때문에, 문서 내에 노드가 많다면 성능측면에서 나빠졌으면 나빠졌지 좋다고 할 수는 없다.

- 여러 번 선언된 **script tag** 는, tag 선언에 대한 문자열이 불필요하게 용량을 차지한다.

- 짧은 블록의 코드는 불필요한 작업들을 반복하도록 할 수 있다.

  >**script 블록에 대한 브라우저의 처리 과정**
  >
  >1. 브라우저가 script tag를 만나면, 닫는 script tag 까지 범위내의 text를 파싱
  >2. 이를 다시 자바스크립트 엔진(인터프리터)이 컴파일하고 그 결과를 다시 parsing stream에 전달
  >3. 해당 블록에 대한 처리가 끝나면, 다시 HTML 파싱을 수행하고, 다시 script tag를 만나면 1번 과정을 반복


<br>
<br>
<br>



# HTTP/2

>  HTTP/2에서는 기존의 일부 성능향상 방법들은 더 이상 필요하지 않게 된다.

### 1. domain sharding

- Multiplexer를 통해 여러 개의 파일을 동시에 한 개의 커넥션을 통해 전송/응답받을 수 있다.

### 2. cookie free 도메인

- 기본적응로 압축을 통한 전송이 프로토콜 레벨에서 지원된다.

### 3. File concatenation, CSS Sprite

- 커넥션이 맺어진 이후, 일정시간 동안 재사용을 위해 커넥션이 유지되기 때문에 http1에서와 같이 handshake 비용을 줄이도록 하기 위한 http request 최소화를 사용할 필요가 없다.



<br>
<br>
<br>



# DOM 핸들링

## 1. DOM 캐싱

- DOM을 접근하는 것은 그 자체만으로 많은 비용을 필요로 한다. 따라서 아래와 같이 따로 저장해 둔 뒤에 사용하는 것이 좋다.

~~~javascript
var name = '';
for(var i=1; i<=10; i++){
  name = document.getElementById("test").nodeName;
}

//DOM 캐싱
var name = '';
var el = document.getElementByID("test");
for(var i=1; i<=10; i++){
  name = el.nodeName;
}
~~~

## 2. innerHTML

- DOM을 이용하여 Element를 생성하고 제거하는 것보다 innerHTML 속성을 사용하는 것이 빠르다.

~~~javascript
var el = $("<div>");
document.body.appendChild(el);
document.body.removeChild(el);

//innerHTML
document.body.innerHTML += "<div></div>";
document.body.innerHTML = "";
~~~

- innerHTML을 이용할 경우 innerHTML에 접근을 최소화 하는 방향으로 작성하는 것이 좋다.

~~~javascript
for(var i=0; i<100; i++) {
  document.body.innerHTML += "<div></div>";
}

//innerHTML에 접근 최소화
var sHTML = "";
for(var i=0; i<100; i++) {
  sHTML += "<div></div>";
}
document.body.innerHTML = sHTML;
~~~