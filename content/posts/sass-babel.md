title: '[week 13] 前端工具之二 - CSS 預處理器、Babel'
author: Heidi Liu
tags:
  - Front-End
  - CSS
  - Babel
categories:
  - Front-End
  - HTML & CSS
date: 2020-09-27 14:20:00
---
> 本篇為 [[FE201] 前端中階：那些前端會用到的工具們](https://lidemy.com/p/fe201) 這門課程的學習筆記。如有錯誤歡迎指正。
<!--more-->
## 前言

在使用新工具之前，大致會依照下列步驟：
1. 安裝工具
2. 閱讀官方文件
3. 更改設定檔

---

## SASS：CSS 預處理器

在進入 CSS 預處理器之前，先談談 CSS 在開發上可能遇到哪些問題：

- 全域會互相干擾
  - 例如：在 index.html 同時引入 main.css 和 normalize.css 可能會互相干擾，不易進行 debug 與維護
- 沒有變數
  - 現在有 CSS Variable
- 沒有辦法組合

因此 CSS 預處理器就誕生了，讓我們能夠以寫程式的方式處理樣式，方便進行維護。

![](https://i.imgur.com/p8sM9vr.png)

### 什麼是 SASS？

#### [SASS（Syntactically Awesome Stylesheets）/ SCSS](https://sass-lang.com/install) 
- 是 CSS 預處理器，能更有結構撰寫程式碼，方便後續維護
- 適合大型專案使用
- SASS 無大括號；SCSS 有，因此後者和 CSS 相容
- CSS 預處理器需另外編譯成 CSS，瀏覽器才能看得懂
> 【註】[SAS（Statistical Analysis System） / SPSS](https://www.sas.com/zh_tw/home.html)：統計分析系統，用來進行數據分析的軟體

### SASS 提供的功能

- 參數與結構化 CSS
  - Nesting：巢狀語法
  - Variables：變數設定
- 模組化 CSS
  - Import
  - Extend
  - Mixin
  - Functions
- 自動化 CSS
  - Condition
  - Loop

### 相關語法

- 安裝 Sass

`npm install -g sass`：`-g` 表示在全域安裝

- 一次性編譯

`sass main.sass main.css`：將 main.sass 檔編譯成 main.css

- watch 模式

`sass --watch main.sass main.css`：每次存檔均會自動進行編譯

- 壓縮功能

`sass --style=compressed mais.sass main.css`：通常是在開發最後才會執行

## Sass 實作補充

main.css 通常由下列要素組成：
1. utils：整理出常用的 variables（背景顏色等）和 mixins（垂直至中、對齊等樣式功能）
2. Components：整理跨頁元件
3. Layouts：獨立的大元件
4. Pages
5. 其他樣式：themes（dark mode）、vendors（bootstrap css）

### 巢狀與變數

1. 巢狀語法：注意 `&` 前的空格

```sass=
.section
  width: 100%
  height: 50%
  border-bottom: 1px solid #000
  padding: 2rem
  &__title
    text-align: center
  &__wrapper
    display: flex
    justify-content: center
    align-items: center
```

編譯結果：

```css=
.section {
  width: 100%;
  height: 50%;
  border-bottom: 1px solid #000;
  padding: 2rem;
}
.section__title {
  text-align: center;
}
.section__wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}

/*# sourceMappingURL=main.css.map */
```

2. 變數設定：使用 `$` 來命名

```sass=
$primary: #0047AB
$secondary: #4D80E6
$warning: #CD5C5C
$text:#CCCCFF
$background: #eeeeee

.color
  width: 50px
  height: 50px
  margin: 10px
  background: #000
  &-primary
    background: $primary
  &-secondary
    background: $secondary
  &-warning
    background: $warning
  &-text
    background: $text
  &-background
    background: $background
```

編譯結果：

```css=
.color {
  width: 50px;
  height: 50px;
  margin: 10px;
  background: #000;
}
.color-primary {
  background: #0047AB;
}
.color-secondary {
  background: #4D80E6;
}
.color-warning {
  background: #CD5C5C;
}
.color-text {
  background: #CCCCFF;
}
.color-background {
  background: #eeeeee;
}

/*# sourceMappingURL=main.css.map */
```

### 模組化 CSS

#### `@import`：引入檔案，用來分別進行管理
  - 例如：將變數放到 `_variables.sass` 統一管理，然後在 `main.sass` 加上 `@import _variables.sass` 即可引入檔案

#### `export`：繼承，處理共同樣式
  - 使用時機：可將所有相同樣式的內容合併，減少重複的行為
  - 例如：`<a>` 統一去除底線、制定 template
```sass=
%btn
  padding: 1rem 2rem
  color: $background
  font-size: 1rem
  margin: 1rem
  transition: .1s

.btn
  &-primary
    @extend %btn
  &-secondary
    @extend %btn
  &-waring
    @extend %btn
```

編譯結果：

```css=
.btn-waring, .btn-secondary, .btn-primary {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
```

#### `mixin`：打包常用功能，替換局部變數

- 使用時機：用於需重複使用到的屬性，且可帶入變數，以進行微調
- 例如：width、height、flex-center 等

```sass=
@mixin btn
  padding: 1rem 2rem
  color: $background
  font-size: 1rem
  margin: 1rem
  transition: .1s

.btn
  &-primary
    +btn
  &-secondary
    +btn
  &-waring
    +btn
```

編譯結果：

```css=
.btn-primary {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
.btn-secondary {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
.btn-waring {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
```

#### `function`：可回傳數值，搭配變數和 mixin 使用
  - 與 `@mixin` 的不同之處，在於 `@function` 只會回傳一個值，而 `@mixin` 是回傳一段CSS程式碼

### Sass 自動化

#### loop 迴圈

- 使用 `@each` 搭配 array
  - 以逗號表示 `center, start, end`

```sass=
$direction-typs: center, start, end

@each $type in $direction-typs
  .flex-#{$type}
    display: flex
    justify-content: $type
    align-items: center
```
編譯結果：
```css=
.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.flex-start {
  display: flex;
  justify-content: start;
  align-items: center;
}

.flex-end {
  display: flex;
  justify-content: end;
  align-items: center;
}
```

- 使用 `@each` 搭配 map
  - 在括號以 `參數:值` 表示 `(center: center, start: flex-start, end: flex-end)`

```sass=
$direction-typs: (center: center, start: flex-start, end: flex-end)

@each $type, $value in $direction-typs
  .flex-#{$type}
    display: flex
    justify-content: $value
    align-items: center
```

編譯結果：

```css=
.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.flex-start {
  display: flex;
  justify-content: flex-start;
  align-items: center;
}

.flex-end {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
```

- for 迴圈
  - 使用時機：用於預先設定，或特效網站需產生密集大小值
```sass=
@for $i from 0 through 5
    .h#{5 - $i + 1}
        font-size: 1 + 0.2rem * $i
```

編譯結果：

```css=
.h6 {
  font-size: 1rem;
}

.h5 {
  font-size: 1.2rem;
}

.h4 {
  font-size: 1.4rem;
}

.h3 {
  font-size: 1.6rem;
}

.h2 {
  font-size: 1.8rem;
}

.h1 {
  font-size: 2rem;
}
```

#### condition

有關 Flow Control 可參考[官方文件](https://sass-lang.com/documentation/at-rules/control/for)。

參考資料：
- [Sass/SCSS 基本語法介紹，搞懂CSS 預處理器](https://tw.alphacamp.co/blog/css-preprocessor-sass-scss)
- [常用色彩表](http://www.ebaomonthly.com/window/photo/lesson/colorList.htm)
- [SCSS 筆記(2) - extend、mixin、function](https://icguanyu.github.io/scss/scss_2/)

### 重構檔案流程

- 步驟一：將重複的部分用巢狀方式撰寫
- 步驟二：將顏色、字體大小，統一使用變數命名 `_variables.sass`
- 步驟三：抽檔案，把重複的區塊放到 `_components.sass`
- 步驟四：將分出的區塊以 `@import` 引入，完成後將所有檔案打包

---

## BABEL：JS 轉譯器

[BABEL](https://babeljs.io/) 是 JavaScript 轉譯器，可將 ES6+ 程式碼轉為等效的 ES5 程式碼。

參考資料：[利用 Babel 支援現代 JavaScript 的特性](https://michaelchen.tech/javascript-programming/babel/)
