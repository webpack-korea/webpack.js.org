---
title: 개념
sort: -1
contributors:
  - TheLarkInn
  - jhnns
  - grgur
  - johnstew
  - jimrfenner
  - TheDutchCoder
  - adambraimbridge
  - EugeneHlushko
  - jeremenichelli
  - arjunsajeev
  - byzyk
  - yairhaimo
  - farskid
  - LukeMwila
  - Jalitha
  - muhmushtaha
  - chenxsan
---

**웹팩**은 자바스크립트 어플리케이션을 위한 _정적 모듈 번들러_ 입니다. 웹팩은 어플리케이션을 처리할 때 프로젝트에 필요한 모든 모듈을 매핑하고 하나 이상의 _번들_ 파일을 생성하는 [종속 그래프]((/concepts/dependency-graph/)를 내부적으로 만듭니다.

<!--
At its core, **webpack** is a _static module bundler_ for modern JavaScript applications. When webpack processes your application, it internally builds a [dependency graph](/concepts/dependency-graph/) which maps every module your project needs and generates one or more _bundles_.
-->

T> [여기에서](/concepts/modules) 자바스크립트 모듈과 웹팩 모듈에 대해 더 자세히 알아보실 수 있습니다.

<!--
T> Learn more about JavaScript modules and webpack modules [here](/concepts/modules).
-->

버전 4.0.0 이후부터는 **웹팩**에서 프로젝트를 번들링할 때 더 이상 설정 파일이 필요하지 않게 되었습니다. 그럼에도 불구하고, 여러분에 더 맞는 설정이 필요하다면 여러분이 여러분의 요구에 맞게 [설정](/configuration)파일을 만들어 적용할 수도 있습니다.

<!--
Since version 4.0.0, **webpack does not require a configuration file** to bundle your project. Nevertheless, it is [incredibly configurable](/configuration) to better fit your needs.
-->

웹팩을 시작하기 위해서 먼저, **핵심 개념**만 이해하시면 됩니다.

<!--
To get started you only need to understand its **Core Concepts**:
-->

- [Entry](#entry)
- [Output](#output)
- [Loaders](#loaders)
- [Plugins](#plugins)
- [Mode](#mode)
- [Browser Compatibility](#browser-compatibility)

이 문서는 이러한 개념에 대한 개괄적인 개요를 제공하는 동시에 상세한 개념별 사용 사례에 대한 링크들을 제공하기 위해 만들어졌습니다.

<!--
This document is intended to give a **high-level** overview of these concepts, while providing links to detailed concept-specific use cases.
-->

모듈 번들러에 대한 아이디어와 모듈 번들러가 내부에서 작동하는 방식을 더 잘 이해하려면 다음 리소스를 참고해주세요.

<!--
For a better understanding of the ideas behind module bundlers and how they work under the hood, consult these resources:
-->

- [Manually Bundling an Application](https://www.youtube.com/watch?v=UNMkLHzofQI)
- [Live Coding a Simple Module Bundler](https://www.youtube.com/watch?v=Gc9-7PBqOC8)
- [Detailed Explanation of a Simple Module Bundler](https://github.com/ronami/minipack)

## 엔트리 (진입점)

엔트리는 내부 [종속 그래프](/concepts/dependency-graph/)를 만들기 위해 웹팩이 어떤 모듈들을 사용해야 하는지를 나타냅니다. 웹팩은 엔트리에 (직접 및 간접적으로) 의존하는 다른 모듈들과 라이브러리를 파악합니다.

기본적으로 적용되는 값은 `./src/index.js`이지만 [웹팩 설정에서 엔트리 속성을 설정](/configuration/entry-context/#entry)하여 다른 (또는 여러 엔트리 포인트들)을 지정할 수 있습니다. 예를 들어:

<!--
## Entry

An **entry point** indicates which module webpack should use to begin building out its internal [dependency graph](/concepts/dependency-graph/). webpack will figure out which other modules and libraries that entry point depends on (directly and indirectly).

By default its value is `./src/index.js`, but you can specify a different (or multiple entry points) by setting an [`entry` property in the webpack configuration](/configuration/entry-context/#entry). For example:
-->

**webpack.config.js**

```js
module.exports = {
  entry: './path/to/my/entry/file.js',
};
```

T> [엔트리 포인트](/concepts/entry-points) 섹션에서 더 많은 내용을 얻을 수 있습니다.

<!--
T> Learn more in the [entry points](/concepts/entry-points) section.
-->

## 아웃풋 (출력)

**아웃풋** 속성은 웹팩에서 생성되는 _번들_ 파일들을 내보낼 위치와 파일 이름을 지정하는 방법을 알려줍니다. 기본적으로, 메인 번들 파일의 경우 `./dist/main.js`로, 웹팩에서 생성된 다른 파일들의 경우 `./dist` 폴더로 기본 설정이 되어 있습니다.

설정 파일에서 아웃풋 필드를 지정하여 이 부분을 재설정할 수 있습니다.

<!--
The **output** property tells webpack where to emit the _bundles_ it creates and how to name these files. It defaults to `./dist/main.js` for the main output file and to the `./dist` folder for any other generated file.

You can configure this part of the process by specifying an `output` field in your configuration:
-->

**webpack.config.js**

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js',
  },
};
```

위의 예에서는 `output.filename`과 `output.path` 속성들을 통해 웹팩에 우리가 사용하고자 하는 번들파일 이름과 번들 파일들을 어디로 내보내기를 원하는지 웹팩에 알려줍니다. 위 예에서, 사용되는 path 모듈은 파일 경로를 조작하는 데 사용되는 핵심 [Node.js 모듈](https://nodejs.org/api/modules.html)입니다.

T> 아웃풋 속성에는 [설정가능한 기능들](/configuration/output)이 훨씬 더 많습니다. 이 개념에 대해 자세히 알아보려면 [아웃풋 섹션](/concepts/output)에서 자세히 읽을 수 있습니다.

<!--
In the example above, we use the `output.filename` and the `output.path` properties to tell webpack the name of our bundle and where we want it to be emitted to. In case you're wondering about the path module being imported at the top, it is a core [Node.js module](https://nodejs.org/api/modules.html) that gets used to manipulate file paths.

T> The `output` property has [many more configurable features](/configuration/output). If you want to learn about the concepts behind it, you can [read more in the output section](/concepts/output).
-->

## 로더

웹팩은 오직 자바스크립트와 JSON 파일만을 이해하며 바로 사용할 수 있는 모듈 번들러입니다. **로더**를 사용하면 웹팩은 다른 유형의 파일들을 처리할 수 있습니다. 다른 유형의 파일들이 어플리케이션에서 사용가능하고 종속성 그래프에 추가할 수 있는 유효한 [모듈](/concepts/modules)로 변환할 수 있습니다.

W> 예를 들어 .css 파일과 같은 유형의 모듈도 가져올 수 있는 기능은 웹팩에 특화된 기능이며 이 기능은 다른 번들러나 다른 태스크 러너들에서는 지원되지 않을 수 있습니다. 우리는 개발자가 웹팩을 사용하게 되었을 때 보다 정확한 의존성 그래프를 만들 수 있기 때문에 언어의 이러한 확장이 보증된다고 느끼고 있습니다.

**로더**는 웹팩 설정안에서 두 가지 속성을 가지고 있습니다.

1. `test`속성은 변환할 파일 또는 파일을 식별합니다.
2. `use`속성은 파일 변환을 할 때 사용되어야 하는 로더들을 지정할 수 있습니다.

<!--
## Loaders

Out of the box, webpack only understands JavaScript and JSON files. **Loaders** allow webpack to process other types of files and convert them into valid [modules](/concepts/modules) that can be consumed by your application and added to the dependency graph.

W> Note that the ability to `import` any type of module, e.g. `.css` files, is a feature specific to webpack and may not be supported by other bundlers or task runners. We feel this extension of the language is warranted as it allows developers to build a more accurate dependency graph.

At a high level, **loaders** have two properties in your webpack configuration:

1. The `test` property identifies which file or files should be transformed.
2. The `use` property indicates which loader should be used to do the transforming.
-->

**webpack.config.js**

```javascript
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
};
```

위의 설정은 'test'와 'use'라는 두 가지 필수 속성을 가진 단일 모듈에 대한 'rules' 속성을 정의했습니다. 이것은 웹팩의 컴파일러에게 다음과 같은 것을 알려줍니다.

> "웹팩 컴파일러야, `require()`/`import` 문 안에서 .txt 파일로 해결되는 경로를 발견하면 **use** `raw-loader`를 사용해서 이 파일을 번들 파일로 추가하기 전에 변환해줘!"

W> 웹팩 설정에서 rules을 정의할 때는 `rules`가 아니라 `module.rules`로 정의하고 있다는 점을 명심해야합니다. 웹팩은 이 작업이 잘못 수행될 경우 경고하게 될 겁니다.

W> 파일을 식별하기 위해 regex를 사용할 때 어떤 파일들은 식별되지 않을 수 있다는 점을 명심해야 합니다. 즉, `/\.txt$/`는 `/\.txt$/'` 또는 `/\.txt$/"`와 같지 않습니다. 전자는 웹팩에 .txt로 끝나는 모든 파일과 일치하도록 지시하고 후자는 웹팩에 절대 경로 '.txt'를 가진 단일 파일을 일치시키도록 지시합니다. 이는 우리의 의도가 아닐 수 있습니다.

사용자의 요구에 맞는 더 다양한 로더 설정에 대해 찾고 싶다면 [로더 섹션](/concepts/loader)에서 찾아주세요.

<!--

The configuration above has defined a `rules` property for a single module with two required properties: `test` and `use`. This tells webpack's compiler the following:

> "Hey webpack compiler, when you come across a path that resolves to a '.txt' file inside of a `require()`/`import` statement, **use** the `raw-loader` to transform it before you add it to the bundle."

W> It is important to remember that when defining rules in your webpack config, you are defining them under `module.rules` and not `rules`. For your benefit, webpack will warn you if this is done incorrectly.

W> Keep in mind that when using regex to match files, you may not quote it. i.e `/\.txt$/` is not the same as `'/\.txt$/'` or `"/\.txt$/"`. The former instructs webpack to match any file that ends with .txt and the latter instructs webpack to match a single file with an absolute path '.txt'; this is likely not your intention.

You can check further customization when including loaders in the [loaders section](/concepts/loaders).

-->

## 플러그인

로더는 특정 유형의 모듈을 변환하는 데 사용되지만 플러그인은 번들 최적화, 에셋(asset) 관리 및 환경 변수 주입과 같은 광범위한 작업을 수행하는 데 활용할 될 수 있습니다.

T> [plugin interface](/api/plugins)와 웹팩의 기능 확장에 사용하는 방법을 확인해주세요.

웹팩의 기능 확장을 위해 플러그인을 사용하려면 `require()`가 필요하며 `plugins` 배열에 추가해야 합니다. 대부분의 플러그인은 옵션을 통해 사용자 정의를 할 수 있습니다. 여러 용도로 구성에서 플러그인을 여러 번 사용할 수 있으며, `new` 연산자를 사용하여 플러그인을 호출하여 인스턴스를 만들어야 합니다.

<!--

## Plugins

While loaders are used to transform certain types of modules, plugins can be leveraged to perform a wider range of tasks like bundle optimization, asset management and injection of environment variables.

T> Check out the [plugin interface](/api/plugins) and how to use it to extend webpack's capabilities.

In order to use a plugin, you need to `require()` it and add it to the `plugins` array. Most plugins are customizable through options. Since you can use a plugin multiple times in a configuration for different purposes, you need to create an instance of it by calling it with the `new` operator.
-->

**webpack.config.js**

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
  plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
};
```

위의 예에서 `html-webpack-plugin`은 생성된 모든 번들 파일들을 자동으로 주입하여 어플리케이션을 위한 HTML 파일을 생성합니다.

T> 여기에 웹팩에서 제공하는 많은 플러그인들이 있습니다! [플러그인 목록](/plugins)을 확인하십시오.

웹팩 설정에서 플러그인을 사용하는 것은 간단합니다. 그러나 더 자세히 살펴볼 수 있는 많은 사용 사례들이 있습니다. [여기서 자세히 알아보세요](/concepts/plugins)

<!--
In the example above, the `html-webpack-plugin` generates an HTML file for your application by injecting automatically all your generated bundles.

T> There are many plugins that webpack provides out of the box! Check out the [list of plugins](/plugins).

Using plugins in your webpack configuration is straightforward. However, there are many use cases that are worth further exploration. [Learn more about them here](/concepts/plugins).
-->

## 모드

모드 파라미터를 `production`, `development`, `none`으로 설정하면 각 환경에 맞는 웹팩에서 내장된 최적화된 설정을 적용하여 어플리케이션을 실행할 수 있다. 기본값은 `production`입니다.

```javascript
module.exports = {
  mode: 'production',
};
```

[mode 설정](/configuration/mode)에 대해 자세히 알아보고 각 값에 대해 어떤 최적화를 수행하는지 알아볼 수 있습니다.

## 브라우저 호환성

webpack은 [ES5 호환](https://kangax.github.io/compat-table/es5/)인 모든 브라우저를 지원합니다.(IE8 이하에서는 지원되지 않음)
웹팩은 ['import()'와 require.sure()](/guides/code-spliting/#dynamic-imports)를 위해 `Promise`가 필요합니다. 이 식을 사용하기 위해 오래된 브라우저를 지원하려면 웹팩을 사용하기 전에 [polyfill](/guides/shimming/)을 로드해야 합니다.

<!--
## Mode

By setting the `mode` parameter to either `development`, `production` or `none`, you can enable webpack's built-in optimizations that correspond to each environment. The default value is `production`.

```javascript
module.exports = {
  mode: 'production',
};
```

Learn more about the [mode configuration here](/configuration/mode) and what optimizations take place on each value.

## Browser Compatibility

webpack supports all browsers that are [ES5-compliant](https://kangax.github.io/compat-table/es5/) (IE8 and below are not supported). webpack needs `Promise` for [`import()` and `require.ensure()`](/guides/code-splitting/#dynamic-imports). If you want to support older browsers, you will need to [load a polyfill](/guides/shimming/) before using these expressions.
-->

## 환경

webpack 5 는 Node.js version 10.13.0+ 이상에서 동작할 수 있습니다.

<!--
## Environment

webpack 5 runs on Node.js version 10.13.0+.
-->
