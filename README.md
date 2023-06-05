# Homepage Optimization

유동균, '웹 개발 스킬을 한 단계 높여 주는 프론트엔드 성능 최적화 가이드', 제 3장 홈페이지 최적화 기반의 실습 및 요약 레포지토리입니다. 크롬 개발자 도구의 Performace, Lighthouse, Network 패널을 이용하여 성능 측정 후 최적화 작업을 진행하였습니다.

## Getting Started

#### install dependencies

```
$ npm install
or
$ yarn
```

#### start development server

```
$ npm run start
or
$ yarn start
```

#### start json-server

```
$ npm run server
or
$ yarn server
```

#### build + serve

```
$ npm run serve
or
$ yarn serve
```

## Problems - Solutions

- [x] Unused Image - Image Lazy Loading (using `IntersectionObserver`)

## `Image Lazy Loading`

이미지 지연 로딩(Lazy Loading)은 웹 페이지의 초기 로딩 시간을 줄이기 위한 기법 중 하나로, 페이지에 있는 이미지들을 모두 한 번에 로딩하는 대신 필요할 때(일반적으로 이미지가 뷰포트에 들어올 때)에만 로딩하는 방법을 의미합니다.

이미지 지연 로딩은 특히 이미지가 많이 포함된 웹 페이지에서 유용합니다. 페이지 로딩 시 사용자에게 처음으로 보여지는 이미지들만 로딩하고, 나머지 이미지는 스크롤 등의 사용자 행동에 따라 로딩하면 초기 로딩 시간을 크게 줄일 수 있습니다.

HTML에는 이러한 지연 로딩을 쉽게 구현할 수 있는 loading 속성이 있습니다. 이 속성을 lazy로 설정하면 브라우저가 자동으로 이미지의 지연 로딩을 처리해줍니다.

```html
<img src="image.jpg" loading="lazy" alt="My Image" />
```

이 외에도 JavaScript를 사용하여 더 세밀한 제어가 가능합니다. Intersection Observer API를 사용하면 웹 페이지의 특정 요소가 뷰포트에 들어오는 시점을 감지하고, 이 시점에 이미지를 로딩하는 코드를 실행할 수 있습니다.

```javascript
document.addEventListener("DOMContentLoaded", function () {
  let lazyImages = [].slice.call(document.querySelectorAll("img.lazy"));

  if ("IntersectionObserver" in window) {
    let lazyImageObserver = new IntersectionObserver(function (
      entries,
      observer
    ) {
      entries.forEach(function (entry) {
        if (entry.isIntersecting) {
          let lazyImage = entry.target;
          lazyImage.src = lazyImage.dataset.src;
          lazyImage.classList.remove("lazy");
          lazyImageObserver.unobserve(lazyImage);
        }
      });
    });

    lazyImages.forEach(function (lazyImage) {
      lazyImageObserver.observe(lazyImage);
    });
  } else {
    // IntersectionObserver가 지원되지 않는 경우에 대한 fallback 코드
  }
});
```

또한, React와 같은 프론트엔드 라이브러리나 프레임워크에서는 react-lazyload, react-loadable 등의 라이브러리를 이용해 이미지 또는 컴포넌트의 지연 로딩을 구현할 수 있습니다.

이미지 지연 로딩은 성능 최적화의 중요한 방법 중 하나이지만, 적절하게 사용되어야 합니다. 만약 모든 이미지에 지연 로딩을 적용하면 사용자가 스크롤할 때마다 이미지 로딩이 발생해 사용자 경험을 해칠 수 있습니다. 따라서, 주요 컨텐츠나 초기에 보여지는 이미지 등은 지연 로딩 대상에서 제외하는 것이 좋습니다.
