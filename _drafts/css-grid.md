url: https://css-tricks.com/auto-sizing-columns-css-grid-auto-fill-vs-auto-fit/
desc: auto-fill vs auto-fill as first argument for repeat()

url: https://medium.com/@elad/css-grid-for-beginners-ee649080529b
desc: Three part series on CSS Grid

CSSGrid
=======
## Internet Exploder

Supported in IE11 (but not grid-gap and grid-template-areas) + use Autoprefixer (-ms-grid, ...)


## Basic Syntax

```scss
.container {
    display: grid;
    grid-template-columns: 300px auto 300px;
    grid-auto-rows: 100px;
    grid-gap: 10px; // or: grid-column-gap & grid-row-gap
}
```

```html
<div class="container">
  <div>300px</div>
  <div>auto width</div>
  <div>300px</div>
</div>
```


## Templates

### Fixed rows/columns

Use `grid-template-columns` and `grid-template-rows`

### Areas  

```scss
.container{
  display: grid;
  grid-template-areas: "header header"
                       "title sidebar"
                       "main sidebar"
                       "footer footer";
}
.top-header {grid-area: header;}
.title-header {grid-area: title;}
.main-content {grid-area: main;}
.sidebar {grid-area: sidebar;}
.footer {grid-area: footer;}
```

```html
<div class="container">
  <header class="top-header">main header</header>
  <h1 class="title-header">page title</h1>
  <main class="main-content">MAIN CONTENT</main>
  <aside class="sidebar">sidebar</aside>
  <footer class="footer">main footer</footer>  
</div>
```

This creates the following layout:  
4 rows, 2 columns with the sidebar (S) vertically spanning the rows title (T) and main (M).  
```
HHH
T S
M S
FFF
```

The different areas are callable by name
```
.popup {
  grid-column: title / sidebar;
  grid-row: main / sidebar;
}
```


## Helpers

**Fraction units**

Use `fr` (fraction) for a proportional unit:
The ratio between cells or rows.

```scss
.container {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
    //                 or: 50% 25% 25%;
}
```

**Responsive with repeat**

```scss
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  // creates 3 columns per row with: 33% 33% 33%
}

@media only screen and (max-width: 1200px) {
    .container {
        grid-template-columns: repeat(2, 1fr); // 2x 50% columns
    }
}
```


**Responsive with minmax**

Repeat grid cells with a minimum width of 300 pixels and a 
maximum of 1fraction (could also use `auto` for same effect)
Cells are stretched to fill available space with `auto-fit`.

```scss
.container {
    display: grid;    
    grid-template-columns: repeat(auto-fit, minmax(300px , 1fr));
}
```

Three columns with the middle one filling up all remaining space.  
A horizontal scrollbar appears as soon as container width < 3x200px.

```scss
.grid {
    display: grid;
    grid-template-columns: 200px minmax(200px, auto) 200px;
}
```

**Fit-Content() and min-content and max-content**  

`min-content` is the minimal width the content can be fitted into.
(ex text breaks on non-breaking spaces while `max-content` does not.

```scss
.container {
    display: grid;
    grid-template-columns: auto min-content auto;
}
```

The middle column takes as little width as possible while still fitting the
content to a maximum of 800px.

`Fit-Content` uses `max-content`
- unless `available < max-content` then it uses `available`
- unless `available < min-content` then it uses `min-content`

```scss
.container {
    display: grid;  
    grid-template-columns: auto fit-content(800px) auto;
}
```


**Exact positioning**  

`.exact-position` is positioned somewhere specifically, all other
cells flow around it as defined by the `grid-template-x`.

```scss
.container {  
  display: grid;
  grid-template-columns: 2fr 1fr 1fr;
  grid-template-rows: 1fr 2fr 2fr;
  grid-auto-flow: dense;
}

.cell-with-exact-position {
   grid-row-start: 2; grid-row-end: 3;
   grid-row: 2 / 3; // equivalent
   
   grid-column-start: 2; grid-column-end: 4;
   grid-column: 2 / 4;
}

.spanning-cell {
   grid-row: span 2;
}
```

`grid-auto-flow: dense` compacts the grid so there are no holes
after using `grid-row` and `grid-column` on cells.


**Carousel**  

```
grid-auto-flow: column;
```

**Alignment properties**  

```
align-items & justify-items = place-items.
align-self & justify-self = place-self.
align-content & justify-content = place-content.
place-items: center; /* = align-items: center; + justify-items: center; */
```



## Questions

**What do we do when people open the website on a resolution with width smaller than the minimum supported one (1024px)**  
Current suggestion? __show dialog: not optimized for this resolution__?

Best to ask the client. I heard there are already tablets that will probably need to be supported.  
Making the design responsive for two devices (md/lg) will make all future development a bit longer plus
some UI rework on existing/working pages will probably be needed, including probably adjusting some
existing/working Partena framework components (Wizards, ...). The developers will probably also need
to make some adhoc decisions about how this should actually look on said tablets unless wireframes 
are updated to include the new md breakpoint.  

Whichever 2 dimensional grid system is used, it will not make all pages/forms/wizards look snappy on
any device automagically. Even if sensible breakpoints were set, without actually testing
on these resolutions things that fit nicely on 1024px might not on xs.

Including support for xs/sm devices will be even more (re)work. It's best to get a definitive
answer whether these should be supported before any rework has started.

As we all know, the longer we wait with adding support for smaller devices, 
the more expensive it will become to "tag it on" later in the SDLC.


**How to add rows dynamically to CSS Grid**  
The problem: `grid-template-rows: 100px 100px 100px ...`  
Use `grid-auto-rows: 100px` 
or use `grid-template-columns: repeat(2, 1fr)`
or use `grid-template-areas`.


**Width of textboxes, dropdowns, ...**  
If you have a grid with responsive cell widths and the form controls are 100% wide,
then the width of the boxes will resize between breakpoints where xs resolutions
have one 100% container width control while md resolutions might have 2-3 controls per row.

**px, em, rem or %**  
- em: Nested ems require some calculating to figure out the actual size
- rem: Fixes that problem. But ems may still be useful for use in a rem container
- px, vw, ...: It depends

```scss
:root {
  font-size: 62.5%; // 16px default, 62.5% of that is 10px = 1rem
}
```


Quote from: https://benfrain.com/just-use-pixels/  
> I feel like nowadays, when choosing the units to specify for
> lengths, it’s just down to authoring preference, team
> communication and weighing up the pros and cons that fixed
> (px) or proportional (em/rem) length units offer.

and

> So I’ve been asking myself the question: when working from a
> visual composite, is the pay off of being able to change the
> root size if needed, worth the difficulties of constantly
> converting values?

or

EM vs REM vs PX – Why you shouldn't “just use pixels”  
https://engageinteractive.co.uk/blog/em-vs-rem-vs-px  
> Depending on the project, who’s developing it, and various other factors
> such as project budgets (we have to be realistic here), pixels might be
> easier and quicker. It also might be easier and quicker to
> ignore accessibility.


or

https://stackoverflow.com/questions/11799236/should-i-use-px-or-rem-value-units-in-my-css  
> tl;dr: use rem




**Paddings, margins, font-sizes**  
They can be responsive using either rem/em or they can be set with a fixed amount of pixels
which might be overruled on a per breakpoint basis.

Bootstrap  
- Switched from px in 3 to rem in 4
- The standard 16px root font-size
- Uses rem for margins, border-radius, shadows, font-size, padding, form controls, ...
- Uses em for padding of badges and borders of spinners (ie 2 times)
- Uses vw/vh for some utility classes only

```
$font-size-base:              1rem !default; // Assumes the browser default, typically `16px`
$font-size-lg:                ($font-size-base * 1.25) !default;
```

Bootstrap header sizes etc are not responsive by default.

Either use media queries to set the body font-size and use rem values everywhere
```
@media (max-width: $screen-xs) {
    body{font-size: 10px;}
}
@media (max-width: $screen-sm) {
    body{font-size: 14px;}
}
```

or use viewport percentages  
```
1vw = 1% of viewport width
1vh = 1% of viewport height
1vmin = 1vw or 1vh, whichever is smaller
1vmax = 1vw or 1vh, whichever is larger
```

Google uses pixels
Foundation5 switched from px to em for media query breakpoints.


Docs: Approach & Page defaults
https://github.com/twbs/bootstrap/blob/6589408a4b91c0f58fa4ac1508d69e3e9e4345e1/site/docs/4.1/content/reboot.md


CSS Grid & Flexbox vs Bootstrap  
- Developers probably already know Bootstrap (CSS Grid & Flexbox not so much)
- CSS Grid for general layout and Flexbox for micro-layouting
- If there are no dedicated or skilled designers, I'd stick with Bootstrap

