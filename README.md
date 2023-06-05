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
- [x] Slow Image loading - Optimize Resource (webp)

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

## `Optimize Image/Video Size`

### `WebP`

WebP는 Google이 개발한 이미지 포맷입니다. JPEG, PNG, GIF 등 기존의 이미지 포맷에 비해 파일 크기를 크게 줄일 수 있으면서도 비슷하거나 더 좋은 품질을 유지할 수 있습니다. 이렇게 하면 웹 페이지의 로딩 속도를 높이고, 대역폭 사용을 줄일 수 있습니다.

WebP는 손실 압축(Lossy compression)과 무손실 압축(Lossless compression) 둘 다 지원합니다.

- 손실 압축 `WebP`: 원본 이미지의 일부 데이터를 생략하여 파일 크기를 줄입니다. 이 때문에 원본 이미지와 정확히 동일하게 복원할 수는 없지만, 품질 손실이 눈에 띄지 않을 정도로 작습니다. 일반적으로 JPEG보다 약 25~34% 작은 파일 크기를 가집니다.
- 무손실 압축 `WebP`: 원본 이미지의 모든 데이터를 유지하면서 파일 크기를 줄입니다. 압축 후에도 원본 이미지와 완전히 동일하게 복원할 수 있습니다. 일반적으로 PNG보다 약 26% 작은 파일 크기를 가집니다.

또한, WebP는 애니메이션과 투명도(알파 채널)도 지원합니다. 이런 면에서는 GIF나 PNG와 같은 기능을 대체할 수 있습니다.

그러나 WebP는 모든 웹 브라우저에서 지원되지는 않습니다. 특히 Internet Explorer나 일부 구형 브라우저에서는 WebP를 지원하지 않습니다. 따라서 웹 사이트에서 WebP 이미지를 사용할 경우에는 브라우저의 지원 여부를 확인하고, 필요에 따라 JPEG나 PNG와 같은 다른 포맷의 이미지를 대체로 제공해야 할 수도 있습니다.

```html
<picture>
  <source data-srcset="{main_items_webp}" type="image/webp" />
  <img data-src="{main_items}" ref="{imagEl1}" />
</picture>
```

#### [Image Converter: Squoosh](https://squoosh.app/)

<details>
<summary>Squoosh Config example</summary>

![image](https://github.com/jiheon788/react-query-realworld/assets/90181028/71caaa06-5474-4281-8db2-2ea9260a1e9b)

</details>

### `WebM`

WebM은 Google이 2010년에 개발한 오픈소스 동영상 파일 포맷입니다. VP8 또는 VP9 비디오 코덱과 Vorbis 또는 Opus 오디오 코덱을 사용하며, HTML5의 `<video>`태그와 잘 호환됩니다.

WebM의 몇 가지 주요 특징은 다음과 같습니다:

1. 높은 압축률: WebM은 고품질의 비디오를 상대적으로 작은 파일 크기로 압축할 수 있습니다. 이는 웹 상에서 비디오를 스트리밍하거나 다운로드할 때 대역폭을 절약하는 데 유용합니다.

2. 로열티-프리: WebM 프로젝트는 모든 소프트웨어와 하드웨어 제조사가 자유롭게 WebM 포맷을 사용할 수 있도록 로열티-프리 라이선스를 제공합니다. 이로 인해 개발자는 추가 비용 없이 WebM을 사용할 수 있습니다.

3. 브라우저 호환성: WebM은 Chrome, Firefox, Opera 등 대부분의 모던 웹 브라우저에서 지원됩니다. 하지만 Safari는 기본적으로 WebM을 지원하지 않으므로 주의가 필요합니다.

WebM은 특히 웹 환경에서 동영상을 효율적으로 제공하고자 할 때 유용합니다. YouTube 같은 동영상 플랫폼에서는 WebM 포맷을 널리 사용하고 있습니다.

```html
<video autoplay loop muted>
  <source src="{video_webm}" type="video/webm" />
  <source src="{video}" type="video/mp4" />
</video>
```

#### [Video Converter: Media.io](https://convert.media.io/app/)

<details>
<summary>Media.io Config example</summary>

![image](https://github.com/jiheon788/react-query-realworld/assets/90181028/f1e7935f-7800-4a49-a6d0-de6a2417fbb0)

</details>
