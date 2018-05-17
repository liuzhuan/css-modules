<img src="https://raw.githubusercontent.com/css-modules/logos/master/css-modules-logo.png" width="150" height="150" />

这是 CSS 模块官方文档，外加简单批注。

CSS Modules 不是官方标准，也没有在浏览器中实现。它位于 webpack 或 browserify 的构建流程中，可以改变类名或选择器名称，使其变得全局唯一。

# CSS Modules （CSS 模块）

A **CSS Module** is a CSS file in which all class names and animation names are scoped locally by default. All URLs (`url(...)`) and `@imports` are in module request format (`./xxx` and `../xxx` means relative, `xxx` and `xxx/yyy` means in modules folder, i. e. in `node_modules`).

> 在 CSS 模块中，所有的类名和动画名默认都是局部的。

CSS Modules compile to a low-level interchange format called ICSS or [Interoperable CSS](https://github.com/css-modules/icss), but are written like normal CSS files:

> CSS 模块将被编译为底层可互换的 ICSS 格式，但是写法同普通 CSS 类似。

``` css
/* style.css */
.className {
  color: green;
}
```

When importing the **CSS Module** from a JS Module, it exports an object with all mappings from local names to global names.

``` js
import styles from "./style.css";
// import { className } from "./style.css";

element.innerHTML = '<div class="' + styles.className + '">';
```

## Naming

For local class names camelCase naming is recommended, but not enforced.

> This is recommended because the common alternative, kebab-casing may cause unexpected behavior when trying to access style.class-name as a dot notation. You can still work around kebab-case with bracket notation (eg. `style['class-name']`) but `style.className` is cleaner.

> 建议类名使用驼峰写法。因为在 JS 中引用时更清晰。

## Exceptions

`:global` switches to global scope for the current selector respective identifier. `:global(.xxx)` respective `@keyframes :global(xxx)` declares the stuff in parenthesis in the global scope.

> `:global` 会切换到全局模式。

Similarly, `:local` and `:local(...)` for local scope.

If the selector is switched into global mode, global mode is also activated for the rules. (This allows us to make `animation: abc;` local.)

Example: `.localA :global .global-b .global-c :local(.localD.localE) .global-d`

## Composition

It's possible to compose selectors.

``` css
.className {
  color: green;
  background: red;
}

.otherClassName {
  composes: className;
  color: yellow;
}
```

> 可以通过 `composes` 组合类。

There can be multiple `composes` rules, but `composes` rules must be before other rules. Extending works only for local-scoped selectors and only if the selector is a single class name. When a class name composes another class name, the **CSS Module** exports both class names for the local class. This can add up to multiple class names.

> `composes` 可有多个，但是务必位于其他规则之前。

It's possible to compose multiple classes with `composes: classNameA classNameB;`.

> 可以使用 `composes: classNameA classNameB;` 组合多个类。

## Dependencies

### Composing from other files

It's possible to compose class names from other **CSS Modules**.

``` css
.otherClassName {
  composes: className from "./style.css";
}
```

> 可以从其他 CSS 模块引入组合。

Note that when composing multiple classes from different files the order of appliance is *undefined*. Make sure to not define different values for the same property in multiple class names from different files when they are composed in a single class.

> 从多个文件引入多个组合，其顺序未知。

Note that composing should not form a circular dependency. Elsewise it's *undefined* whether properties of a rule override properties of a composed rule. The module system may emit an error.

> 务必不要让组合形成环形依赖。

Best if classes do a single thing and dependencies are hierarchic.

### Composing from global class names

It's possible to compose from **global** class names.

```css
.otherClassName {
  composes: globalClassName from global;
}
```

> 可以从全局类名组合出新类。

## Usage with preprocessors

Preprocessors can make it easy to define a block global or local.

i. e. with less.js

``` less
:global {
  .global-class-name {
    color: green;
  }
}
```

可以使用预处理器简化 global 或 local 的声明。

## Why?

**modular** and **reusable** CSS!

* No more conflicts.
* Explicit dependencies.
* No global scope.

## Examples

* [css-modules/webpack-demo](https://github.com/css-modules/webpack-demo)
* [outpunk/postcss-modules-example](https://github.com/outpunk/postcss-modules-example)
* [Theming](docs/theming.md)
* [css-modules/browserify-demo](https://github.com/css-modules/browserify-demo)
* [x-team/starting-css-modules](https://github.com/x-team/starting-css-modules)

## History

* 04/2015: `placeholders` feature in css-loader (webpack) allows local scoped selectors (later renamed to `local scope`) by @sokra
* 05/2015: `postcss-local-scope` enables `local scope` by default (see [blog post](https://medium.com/seek-ui-engineering/the-end-of-global-css-90d2a4a06284)) by @markdalgleish
* 05/2015: `extends` feature in css-loader allow to compose local or imported class names by @sokra
* 05/2015: First CSS Modules spec document and github organization with @sokra, @markdalgleish and @geelen
* 06/2015: `extends` renamed to `composes`
* 06/2015: PostCSS transformations to transform CSS Modules into an intermediate format (ICSS)
* 06/2015: Spec for ICSS as common implementation format for multiple module systems by @geelen
* 06/2015: Implementation for jspm by @geelen and @guybedford
* 06/2015: Implementation for browserify by @joshwnj, @joshgillies and @markdalgleish
* 06/2015: webpack's css-loader implementation  updated to latest spec by @sokra


## Implementations

### webpack

Webpack's [css-loader](https://github.com/webpack/css-loader) in module mode replaces every local-scoped identifier with a global unique name (hashed from module name and local identifier by default) and exports the used identifier.

> Webpack 的 css-loader 处于 module 模式，会把每个局部变量替换为全局唯一名称。

Extending adds the source class name(s) to the exports.

Extending from other modules first imports the other module and then adds the class name(s) to the exports.

### Server-side and static websites

[PostCSS-Modules](https://github.com/outpunk/postcss-modules) allows to use CSS Modules for static builds and the server side with Ruby, PHP or any other language or framework.

## REF

- [CSS Modules - Welcome to the Future][glen], by *Glen Maddern*, 2015/08/19
- [Understanding the CSS Modules Methodology][sitepoint], by *Hugo Giraudel*, 2015/12/16
- [CSS Modules 详解及 React 中实践][camsong-css-modules] by *Cam Song*, 2015/12/31
- [part1 - What are CSS Modules and why do we need them?][css-tricks], by *Robin Rendle*, 2016/04/04
- [part2 - Getting Started with CSS Modules][css-tricks-2], by *Robin Rendle*, 2016/04/11
- [part3 - CSS Modules and React][css-tricks-3], by *Robin Rendle*, 2016/05/23
- [CSS Modules 用法教程][ruanyifeng], by *阮一峰*, 2016/06/10

[camsong-css-modules]: https://github.com/camsong/blog/issues/5
[css-tricks]: https://css-tricks.com/css-modules-part-1-need/
[css-tricks-2]: https://css-tricks.com/css-modules-part-2-getting-started/
[css-tricks-3]: https://css-tricks.com/css-modules-part-3-react/
[glen]: https://glenmaddern.com/articles/css-modules
[sitepoint]: https://www.sitepoint.com/understanding-css-modules-methodology/
[ruanyifeng]: http://www.ruanyifeng.com/blog/2016/06/css_modules.html