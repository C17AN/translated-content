---
title: CSS 성능 최적화
slug: Learn/Performance/CSS
page-type: learn-module-chapter
---

{{LearnSidebar}}{{PreviousMenuNext("Learn/Performance/html", "Learn/Performance/business_case_for_performance", "Learn/Performance")}}

When developing a website, you need to consider how the browser is handling the CSS on your site. To mitigate any performance issues that CSS might be causing, you should optimize it. For example, you should optimize the CSS to mitigate [render-blocking](/ko/docs/Glossary/Render_blocking) and minimize the number of required reflows. This article walks you through key CSS performance optimization techniques.

웹사이트를 개발할 때는 브라우저가 사이트의 CSS를 어떻게 다루고 있는지를 고려해야 합니다. CSS가 원인이 되어 발생할 수 있는 성능 문제를 완화하기 위해서는, CSS를 최적화해야 합니다. 예를 들어, [render-blocking](/ko/docs/Glossary/Render_blocking)을 완화하고 필요한 리플로우 횟수를 최소화하려면 CSS를 최적화해야 합니다. 이 글은 핵심 CSS 성능 최적화 기법을 안내합니다.

<table>
  <tbody>
    <tr>
      <th scope="row">준비물:</th>
      <td>
        기본 컴퓨터 활용 능력,
        <a
          href="/ko/docs/Learn/Getting_started_with_the_web/Installing_basic_software"
          >기본 소프트웨어들이 설치된 상태</a
        >와
        <a href="/ko/docs/Learn/Getting_started_with_the_web"
          >웹 클라이언트 기술</a
        >에 대한 기초적인 지식
      </td>
    </tr>
    <tr>
      <th scope="row">목표:</th>
      <td>
        CSS가 웹사이트 성능에 미치는 영향과 성능을 향상시키기 위한 CSS 최적화 방법 습득하기
      </td>
    </tr>
  </tbody>
</table>

## To optimize or not to optimize

## 최적화하거나, 하지 않거나

CSS 최적화를 시작하기 전에 답해야 할 첫 번째 질문은 "무엇을 최적화해야 하는가?" 입니다. 이제부터 설명할 일부 팁과 기법들은 거의 모든 웹 프로젝트에서도 도움이 될 좋은 습관이지만, 반면 일부는 특정 상황에서만 필요한 것들일 수도 있습니다. 모든 기법들을 적용하려 시도하는 것은 어쩌면 불필요하며 시간을 낭비하는 일일 수도 있습니다. 프로젝트에서 실제로 필요로 하는 성능 최적화를 파악하세요.

To do this, you need to [measure the performance](/ko/docs/Learn/Performance/Measuring_performance) of your site. As the previous link shows, there are several different ways to measure performance, some involving sophisticated [performance APIs](/ko/docs/Web/API/Performance_API). The best way to get started however, is to learn how to use tools such as built-in browser [network](/ko/docs/Learn/Performance/Measuring_performance#network_monitor_tools) and [performance](/ko/docs/Learn/Performance/Measuring_performance#performance_monitor_tools) tools, to see what parts of the page load are taking a long time and need optimizing.

이를 위해서는 사이트의 [성능 측정](/ko/docs/Learn/Performance/Measuring_performance) 이 필요합니다. 이 링크에서 보여주는 것처럼 성능을 측정하기 위한 방법은

## Optimizing rendering

Browsers follow a specific rendering path — paint only occurs after layout, which occurs after the render tree is created, which in turn requires both the DOM and the CSSOM trees.

Showing users an unstyled page and then repainting it after the CSS styles have been parsed would be a bad user experience. For this reason, CSS is render blocking until the browser determines that the CSS is required. The browser can paint the page after it has downloaded the CSS and built the [CSS object model (CSSOM)](/ko/docs/Glossary/CSSOM).

To optimize the CSSOM construction and improve page performance, you can do one or more of the following based on the current state of your CSS:

- **Remove unnecessary styles**: This may sound obvious, but it is surprising how many developers forget to clean up unused CSS rules that were added to their stylesheets during development and ended up not being used. All styles get parsed, whether they are being used during layout and painting or not, so it can speed up page rendering to get rid of unused ones. As [How Do You Remove Unused CSS From a Site?](https://css-tricks.com/how-do-you-remove-unused-css-from-a-site/) (csstricks.com, 2019) summarizes, this is a difficult problem to solve for a large codebase, and there isn't a magic bullet to reliably find and remove unused CSS. You need to do the hard work of keeping your CSS modular and being careful and deliberate about what is added and removed.

- **Split CSS into separate modules**: Keeping CSS modular means that CSS not required at page load can be loaded later on, reducing initial CSS render-blocking and loading times. The simplest way to do this is by splitting up your CSS into separate files and loading only what is needed:

  ```html
  <!-- Loading and parsing styles.css is render-blocking -->
  <link rel="stylesheet" href="styles.css" />

  <!-- Loading and parsing print.css is not render-blocking -->
  <link rel="stylesheet" href="print.css" media="print" />

  <!-- Loading and parsing mobile.css is not render-blocking on large screens -->
  <link
    rel="stylesheet"
    href="mobile.css"
    media="screen and (max-width: 480px)" />
  ```

  The above example provides three sets of styles — default styles that will always load, styles that will only be loaded when the document is being printed, and styles that will be loaded only by devices with narrow screens. By default, the browser assumes that each specified style sheet is render-blocking. You can tell the browser when a style sheet should be applied by adding a `media` attribute containing a [media query](/ko/docs/Web/CSS/CSS_media_queries/Using_media_queries). When the browser sees a style sheet that it only needs to apply in a specific scenario, it still downloads the stylesheet, but doesn't render-block. By separating the CSS into multiple files, the main render-blocking file, in this case `styles.css`, is much smaller, reducing the time for which rendering is blocked.

- **Minify and compress your CSS**: Minifying involves removing all the whitespace in the file that is only there for human readability, once the code is put into production. You can reduce loading times considerably by minifying your CSS. Minification is generally done as part of a build process (for example, most JavaScript frameworks will minify code when you build a project ready for deployment). In addition to minification, make sure that the server that your site is hosted on uses compression such as gzip on files before serving them.

- **Simplify selectors**: People often write selectors that are more complex than needed for applying the required styles. This not only increases file sizes, but also the parsing time for those selectors. For example:

  ```css
  /* Very specific selector */
  body div#main-content article.post h2.headline {
    font-size: 24px;
  }

  /* You probably only need this */
  .headline {
    font-size: 24px;
  }
  ```

  Making your selectors less complex and specific is also good for maintenance. It is easy to understand what simple selectors are doing, and it is easy to override styles when needed later on if the selectors are less [specific](/ko/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance#specificity_2).

- **Don't apply styles to more elements than needed**: A common mistake is to apply styles to all elements using the [universal selector](/ko/docs/Web/CSS/Universal_selectors), or at least, to more elements than needed. This kind of styling can impact performance negatively, especially on larger sites.

  ```css
  /* Selects every element inside the <body> */
  body * {
    font-size: 14px;
    display: flex;
  }
  ```

  Remember that many properties (such as {{cssxref("font-size")}}) inherit their values from their parents, so you don't need to apply them everywhere. And powerful tools such as [Flexbox](/ko/docs/Learn/CSS/CSS_layout/Flexbox) need to be used sparingly. Using them everywhere can cause all kinds of unexpected behavior.

- **Cut down on image HTTP requests with CSS sprites**: [CSS sprites](https://css-tricks.com/css-sprites/) is a technique that places several small images (such as icons) that you want to use on your site into a single image file, and then uses different {{cssxref("background-position")}} values to display the chunk of image that you want to show in each different place. This can dramatically cut down on the number of HTTP requests needed to fetch the images.

- **Preload important assets**: You can use [`rel="preload"`](/ko/docs/Web/HTML/Attributes/rel/preload) to turn {{htmlelement("link")}} elements into preloaders for critical assets. This includes CSS files, fonts, and images:

  ```html
  <link rel="preload" href="style.css" as="style" />

  <link
    rel="preload"
    href="ComicSans.woff2"
    as="font"
    type="font/woff2"
    crossorigin />

  <link
    rel="preload"
    href="bg-image-wide.png"
    as="image"
    media="(min-width: 601px)" />
  ```

  With `preload`, the browser will fetch the referenced resources as soon as possible and make them available in the browser cache so that they will be ready for use sooner when they are referenced in subsequent code. It is useful to preload high-priority resources that the user will encounter early on in a page so that the experience is as smooth as possible. Note how you can also use `media` attributes to create responsive preloaders.

  See also [Preload critical assets to improve loading speed](https://web.dev/articles/preload-critical-assets) on web.dev (2020)

## Handling animations

Animations can improve perceived performance, making interfaces feel snappier and making users feel like progress is being made when they are waiting for a page to load (loading spinners, for example). However, larger animations and a higher number of animations will naturally require more processing power to handle, which can degrade performance.

The simplest advice is to cut down on all unnecessary animations. You could also provide users with a control/site preference to turn off animations if they are using a low-powered device or a mobile device with limited battery power. You could also use JavaScript to control whether or not animation is applied to the page in the first place. There is also a media query called [`prefers-reduced-motion`](/ko/docs/Web/CSS/@media/prefers-reduced-motion) that can be used to selectively serve animation styles or not based on a user's OS-level preferences for animation.

For essential DOM animations, you are advised to use [CSS animations](/ko/docs/Web/CSS/CSS_animations/Using_CSS_animations) where possible, rather than JavaScript animations (the [Web Animations API](/ko/docs/Web/API/Web_Animations_API) provides a way to directly hook into CSS animations using JavaScript).

### Choosing properties to animate

Next, animation performance relies heavily on what properties you are animating. Certain properties, when animated, trigger a [reflow](/ko/docs/Glossary/Reflow) (and therefore also a [repaint](/ko/docs/Glossary/Repaint)) and should be avoided. These include properties that:

- Alter an element's dimensions, such as [`width`](/ko/docs/Web/CSS/width), [`height`](/ko/docs/Web/CSS/height), [`border`](/ko/docs/Web/CSS/border), and [`padding`](/ko/docs/Web/CSS/padding).
- Reposition an element, such as [`margin`](/ko/docs/Web/CSS/margin), [`top`](/ko/docs/Web/CSS/top), [`bottom`](/ko/docs/Web/CSS/bottom), [`left`](/ko/docs/Web/CSS/left), and [`right`](/ko/docs/Web/CSS/right).
- Change an element's layout, such as [`align-content`](/ko/docs/Web/CSS/align-content), [`align-items`](/ko/docs/Web/CSS/align-items), and [`flex`](/ko/docs/Web/CSS/flex).
- Add visual effects that change the element geometry, such as [`box-shadow`](/ko/docs/Web/CSS/box-shadow).

Modern browsers are smart enough to repaint only the changed area of the document, rather than the entire page. As a result, larger animations are more costly.

If at all possible, it is better to animate properties that do not cause reflow/repaint. This includes:

- [Transforms](/ko/docs/Web/CSS/CSS_transforms)
- [`opacity`](/ko/docs/Web/CSS/opacity)
- [`filter`](/ko/docs/Web/CSS/filter)

### Animating on the GPU

To further improve performance, you should consider moving animation work off the main thread and onto the device's GPU (also referred to as compositing). This is done by choosing specific types of animations that the browser will automatically send to the GPU to handle; these include:

- 3D transform animations such as [`transform: translateZ()`](/ko/docs/Web/CSS/transform) and [`rotate3d()`](/ko/docs/Web/CSS/transform-function/rotate3d).
- Elements with certain other properties animated such as [`position: fixed`](/ko/docs/Web/CSS/position).
- Elements with [`will-change`](/ko/docs/Web/CSS/will-change) applied (see the section below).
- Certain elements that are rendered in their own layer, including [`<video>`](/ko/docs/Web/HTML/Element/video), [`<canvas>`](/ko/docs/Web/HTML/Element/canvas), and [`<iframe>`](/ko/docs/Web/HTML/Element/iframe).

Animation on the GPU can result in improved performance, especially on mobile. However, moving animations to GPU is not always that simple. Read [CSS GPU Animation: Doing It Right](https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/) (smashingmagazine.com, 2016) for a very useful and detailed analysis.

## Optimizing element changes with `will-change`

Browsers may set up optimizations before an element is actually changed. These kinds of optimizations can increase the responsiveness of a page by doing potentially expensive work before it is required. The CSS [`will-change`](/ko/docs/Web/CSS/will-change) property hints to browsers how an element is expected to change.

> **Note:** `will-change` is intended to be used as a last resort to try to deal with existing performance problems. It should not be used to anticipate performance problems.

```css
.element {
  will-change: opacity, transform;
}
```

## Optimizing for render blocking

CSS can scope styles to particular conditions with media queries. Media queries are important for a responsive web design and help us optimize a critical rendering path. The browser blocks rendering until it parses all of these styles but will not block rendering on styles it knows it will not use, such as the print stylesheets. By splitting the CSS into multiple files based on media queries, you can prevent render blocking during download of unused CSS. To create a non-blocking CSS link, move the not-immediately used styles, such as print styles, into separate file, add a [`<link>`](/ko/docs/Web/HTML/Element/link) to the HTML mark up, and add a media query, in this case stating it's a print stylesheet.

```html
<!-- Loading and parsing styles.css is render-blocking -->
<link rel="stylesheet" href="styles.css" />

<!-- Loading and parsing print.css is not render-blocking -->
<link rel="stylesheet" href="print.css" media="print" />

<!-- Loading and parsing mobile.css is not render-blocking on large screens -->
<link
  rel="stylesheet"
  href="mobile.css"
  media="screen and (max-width: 480px)" />
```

By default, the browser assumes that each specified style sheet is render blocking. Tell the browser when the style sheet should be applied by adding a `media` attribute with the [media query](/ko/docs/Web/CSS/CSS_media_queries/Using_media_queries). When the browser sees a style sheet it knows that it only needs to apply it for a specific scenario, it still downloads the stylesheet, but doesn't render block. By separating out the CSS into multiple files, the main render-blocking file, in this case `styles.css`, is much smaller, reducing the time that rendering is blocked.

## Improving font performance

This section contains some useful tips for improving web font performance.

In general, think carefully about the fonts you use on your site. Some font files can be very large (multiple megabytes). While it can be tempting to use lots of fonts for visual excitement, this can slow down page load significantly, and cause your site to look like a mess. You probably only need about two or three fonts, and you can get away with less if you choose to use [web safe fonts](/ko/docs/Learn/CSS/Styling_text/Fundamentals#web_safe_fonts).

### Font loading

Bear in mind that a font is only loaded when it is actually applied to an element using the [`font-family`](/ko/docs/Web/CSS/font-family) property, not when it is first referenced using the [`@font-face`](/ko/docs/Web/CSS/@font-face) at-rule:

```css
/* Font not loaded here */
@font-face {
  font-family: "Open Sans";
  src: url("OpenSans-Regular-webfont.woff2") format("woff2");
}

h1,
h2,
h3 {
  /* It is actually loaded here */
  font-family: "Open Sans";
}
```

It can therefore be beneficial to use `rel="preload"` to load important fonts early, so they will be available more quickly when they are actually needed:

```html
<link
  rel="preload"
  href="OpenSans-Regular-webfont.woff2"
  as="font"
  type="font/woff2"
  crossorigin />
```

This is more likely to be beneficial if your `font-family` declaration is hidden inside a large external stylesheet, and won't be reached until significantly later in the parsing process. It is a tradeoff however — font files are quite large, and if you preload too many of them, you may delay other resources.

You can also consider:

- Using [`rel="preconnect"`](/ko/docs/Web/HTML/Attributes/rel/preconnect) to make an early connection with the font provider. See [Preconnect to critical third-party origins](https://web.dev/articles/font-best-practices#preconnect_to_critical_third-party_origins) for details.
- Using the [CSS Font Loading API](/ko/docs/Web/API/CSS_Font_Loading_API) to customize the font loading behavior via JavaScript.

### Loading only the glyphs you need

When choosing a font for body copy, it is harder to be sure of the glyphs that will be used in it, especially if you are dealing with user-generated content and/or content across multiple languages.

However, if you know you are going to use a specific set of glyphs (for example, glyphs for headings or specific punctuation characters only), you could limit the number of glyphs the browser has to download. This can be done by creating a font file that only contains the required subset. A process called [subsetting](https://fonts.google.com/knowledge/glossary/subsetting). The [`unicode-range`](/ko/docs/Web/CSS/@font-face/unicode-range) `@font-face` descriptor can then be used to specify when your subset font is used. If the page doesn't use any character in this range, the font is not downloaded.

```css
@font-face {
  font-family: "Open Sans";
  src: url("OpenSans-Regular-webfont.woff2") format("woff2");
  unicode-range: U+0025-00FF;
}
```

### Defining font display behavior with the `font-display` descriptor

Applied to the `@font-face` at-rule, the [`font-display`](/ko/docs/Web/CSS/@font-face/font-display) descriptor defines how font files are loaded and displayed by the browser, allowing text to appear with a fallback font while a font loads, or fails to load. This improves performance by making the text visible instead of having a blank screen, with a trade-off being a flash of unstyled text.

```css
@font-face {
  font-family: someFont;
  src: url(/path/to/fonts/someFont.woff) format("woff");
  font-weight: 400;
  font-style: normal;
  font-display: fallback;
}
```

## Optimizing styling recalculation with CSS containment

By using the properties defined in the [CSS containment](/ko/docs/Web/CSS/CSS_containment) module, you can instruct the browser to isolate different parts of a page and optimize their rendering independently from one another. This allows for improved performance in rendering individual sections. As an example, you can specify to the browser to not render certain containers until they are visible in the viewport.

The {{cssxref("contain")}} property allows an author to specify exactly what containment types they want applied to individual containers on the page. This allows the browser to recalculate layout, style, paint, size, or any combination of them for a limited part of the DOM.

```css
article {
  contain: content;
}
```

The {{cssxref("content-visibility")}} property is a useful shortcut, which allows authors to apply a strong set of containments on a set of containers and specify that the browser should not lay out and render those containers until needed.

A second property, {{cssxref("contain-intrinsic-size")}}, is also available, which allows you to provide a placeholder size for containers while they are under the effects of containment. This means that the containers will take up space even if their contents have not yet been rendered, allowing containment to do its performance magic without the risk of scroll bar shift and jank as elements render and come into view. This improves the quality of the user experience as the content is loaded.

```css
article {
  content-visibility: auto;
  contain-intrinsic-size: 1000px;
}
```

{{PreviousMenuNext("Learn/Performance/html", "Learn/Performance/business_case_for_performance", "Learn/Performance")}}

## See also

- [CSS animation performance](/ko/docs/Web/Performance/CSS_JavaScript_animation_performance)
- [Best practices for fonts](https://web.dev/articles/font-best-practices) on web.dev (2022)
- [content-visibility: the new CSS property that boosts your rendering performance](https://web.dev/articles/content-visibility) on web.dev (2022)
