---
layout: post
title: HTML, CSS, and Javascript for Web Developers Week2
subtitle: Cascading Style Sheets Basics
date: 2017-01-09
categories: Coursera
tags: [Web]
catalog: true
---

### Cascading Style Sheets Basics

#### Anatomy of a CSS rule

Selector: {

​	Property1:  value1; -> declaration

​	Property2: value2;

}. -> in Style sheet

Each browser has its own style -> override.

#### Elements, class and ID selectors

- Element selector: style of all elements with that element's name

  ```css
  /* all h2 elements */
  h2 {
    color: red;
    text-align: center;
  }
  ```

- Class selector: an attribute class = class's name  

  Define with '.',  BTW class use without '.'

  ```css
  /* all with class="highlight" */
  .highlight {
    font-size: 20px;
    font-weight: bold;
    font-style: italic;
    background-color: green;
    opacity: .6;
  }
  ```

- id selector: element with that id

  Define with '#'

  ```css
  /* element with id="mainPoint" */
  #mainPoint {
    font-size: 24px;
    font-weight: bold;
    background-color: red;
    opacity: .7;
  }
  ```

  One id only appear once so the css for id is not so reusable

- Grouping selectors: grouping by ','

  ```css
  /* all p AND h1 elements */
  p, h1 {
    color: blue;
    text-align: center;
  }
  ```

#### Combining selectors

- Element with class selector: selector.class

```css
/*no space between element and class*/
/* all p elements with class="big" */
p.big{
  front-size: 20px;
}
```

- Child selector: selector > selector 

```css
/* only direct child will be affect */
article > p {
  color: blue;
}
```

- Descendant selector: selector selector

```css
/* all descendant contains will be affect, as long as it is inside */
aritcle p {
  color: blue;
}
```

- No limited to Element selector

```css
/* every P that is inside at any level an element with class="colored" will be affect */
.color p{
  color: blue;
}
```

```css
/* every element with class="colored" that is direct child of article element will be affect */
article > .colored{
  color: blue;
}
```

#### Peseudo-class selectors

- :link  未访问

  ```css
  /* visited means after visit it can have different style */
  a:link, a:visited {
  }
  ```

- :visited  已访问

- :hover   悬浮

  ```css
  a:hover, a:active {
    background-color: red;
    color: purple;
  }
  ```

- :active  活动

- :nth-child(…)  第几个link

  ```css
  section div:nth-child(odd):hover {
    background-color: green;
    cursor: pointer;
  }
  ```

Block elements fill the background of the all line and inline elements only cover the words.

Well, 还是觉得自己写代码比较有用...毕竟learn by practice

#### Style placement

- Inline style: least resuable

- Head styles \<style>\</style> : often override external ones 👇

- external style sheets -> always used

  ```css
  <link rel="stylesheet" herf="stylesheet.css">
  ```

#### Conflict resolution

- Origin: last declaration wins: top to bottom, last external declaration

- Merge: element gets both style

- Inheritance: every child or grandchild inherit the parent style

- Specificity: most specific selector combination wins

  Specific Score: **style="", ID, Class | pseudo-class| attribute, # of Elements**  from left to right get less specific. 

- **!important** no matter how specific score, use this style

#### Styling text:

```css
.style{
  font-family: /* 字体 */;
  color: /*RGB*/ ;
  font-style: italic; /*normal, 斜体*/
  font-weight: bold; /*normal, 加粗*/
  font-size: 24px; 
  /*120% - 1.2 of current*/ 
  /*2em - twice relative to current style*/
  /* .5em - half of current size */
  text-transform: lowercase;
  text-align: center;
}
```

Relative size: % and em

#### The box model

- Margin: 👇

  **Cumulative** from left to right(sum), but **collapse** from above to below(larger one).

- Border: 👇

- Padding: padding around ➡️ content

   Box size = conent + padding + border

   width is setting **content box**

- box-sizing: border-box; -> set size of border box

  Box sizing is **not** inherit.

- Universal 👇

```css
* { } /* apply to all elements */
```

- overflow: visiable; hidden; auto (a scroll ball); scroll;

#### The background property

🌰：

```css
#bg {
  width: 500px;
  height: 500px;
  /* sub-property will be override by background property*/
  background-color: blue; 
  background: url('path.png') /* relative path to css file */
  background-repeat: no-repeat top center;
  /* same as */
  background: url("path.png") no-repeat right center blue;
}

```

#### Position elements by floating

这周的课略多。。。？😰

- clear: resume the regular document flow -> a newline
- clear: left; right; both; -> no cover
- percentage: flexible with page change
- floats don't have vertical margin collpse

#### Relative and absolute element positioning

- static positioning: normal document flow, default for all elements except for html.

- relative positioning: relative to its position in **normal document flow**

  Offset: top, bottom, left, right. (From...)

  Origional position is **preserved**.

- absolute positioning: all offset is relative to the **nearest ancestor** has a **relative or absolute** position, set to non-static value

  Elements is taken out of normal document flow.

- **relative container** elements moving, all within will move as well.

#### Introduction to responsive design

- Basic media query syntax

  @media(media feature)

  @media(media feature) logical operator(media feature)

- not to overlap breakpoints

- usually base styling -> media query

#### Responsive design

- Denifination: site designed to adapt its layout to the viewing environment by using fluid, proportion-based grids, flexible imgs and CSS3 media queries -> using %

  Layout adapts to device.

  Content verbosity or visual delivery may change.

- 12-column grid responsive layout -> usually used

  1\*12, 3\*4, 2\*6 -> easy to divide

  Use % to achieve fluid width

- Viewport meta tag to turn off default mobile zooming

#### Introduction to twitter bootstrap

突然发现这课的assignment打不开…什么鬼啦！

- Bootstrap: most popular HTML, CSS, and JS framework for developing responsive, mobile first projects on the web.
- Mobile first == PLAN mobile from the start.
- CSS framework is mobile ready.

#### Bootscrap grid system basics

```html
<!-- must be inside container or container-fluid -->
<div class="container"> 
<!-- Creates horizontal groups of colums, applies negative left/right margins so the colums content will align perfectly with the regular content which is still in the container -->
  <div class="row">
<!-- column class template: col-SIZE-SPAN -->
    <div class="col-md-4">Col 1</div>
<!-- md is a sizing identifier that stands for some break point, means when the columns will start collapsing one up to the other-->
  </div>
</div>
```

**col-SIZE-SPAN template:** 

Screen width range identifier.

Columns will collapse below that width(SIZE), unless another rule applies.

If no other rules applied, specifying col-xs0… we will keep that layout no matter what size of screen.

SPAN means how many columns element should span. (1-12)

这课东西还挺多，要是作业现在就能打开就更好了。

