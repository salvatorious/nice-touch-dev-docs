## Gravity Forms
Gravity forms documentation is pretty good, here's a link to customizing the output structure.
**[Gravity Forms filter documentation](https://docs.gravityforms.com/category/extending-gravity-forms/hooks/filters/)**


## Page Jump anchors
Use headline fields to automatically make sections linkable  
**Note:** this assumes that the `slugify` utility has been loaded in the theme  
`<a id="{!! slugify(get_sub_field('headline')) !!}" class="anchor"></a>`
```css
a.anchor { // offset for page jumps so content has breathing room
  display: block;
  position: relative;
  top: -80px;
  visibility: hidden;
}
```


## Viewport entry animation classes
1. Install animation library: `yarn add gsap`  
2. Install viewport entry library: `yarn add in-view`  
3. In main.js:   
```
const TweenMax = require('gsap'); 
const inView = require('in-view');

inView('.fade-in')
    .on('enter', (el) => {
      TweenMax.to(el, 0.6, {autoAlpha: 1, delay: 0.2});
    })
    .on('exit', (el) => {
      // el.style.opacity = 0.5;
    });
```
4. In _animations.scss: 
```
.fade-in { // Needs js body class check
  @include respond-to(large) {
    opacity: 0;
  }
}

.fade-in-up { // Needs js body class check
  @include respond-to(large) {
    opacity: 0;
    transform: matrix(1, 0, 0, 1, 0, 80); // vertical offset
  }
}
```
5. Add classes to markup  
6. Make more cool animation classes!

