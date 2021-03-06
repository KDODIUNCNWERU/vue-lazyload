# Vue-Lazyload

Vue module for lazyloading images in your applications. Some of goals of this project worth noting include:

* Be lightweight, powerful and easy to use
* Work on any image type
* Add loading class while image is loading
* Supports both of Vue 1.0 and Vue 2.0



# Table of Contents

* [___Demo___](#demo)
* [___Requirements___](#requirements)
* [___Installation___](#installation)
* [___Usage___](#usage)  
 * [___Constructor Options___](#constructor-options)
 * [___Implementation___](#implementation)
    * [___Basic___](#basic)
    * [___Css state___](#css-state)
* [___Methods___](#methods)
  * [__Event hook__](#event-hook)
  * [__LazyLoadHandler__](#lazyloadhandler)
  * [__Performance__](#performance)
* [___Authors && Contributors___](#authors-&&-Contributors)
* [___License___](#license)


# Demo

[___Demo___](http://hilongjw.github.io/vue-lazyload/)

# Requirements

- [Vue.js](https://github.com/vuejs/vue) `1.x` or `2.x`  


# Installation

```bash

# npm
$ npm install vue-lazyload

```

# Usage

main.js

```javascript

import Vue from 'vue'
import App from './App.vue'
import VueLazyload from 'vue-lazyload'

Vue.use(VueLazyload)

// or with options
Vue.use(VueLazyload, {
  preLoad: 1.3,
  error: 'dist/error.png',
  loading: 'dist/loading.gif',
  attempt: 1
})

new Vue({
  el: 'body',
  components: {
    App
  }
})
```

## Constructor Options

|key|description|default|options|
|:---|---|---|---|
| `preLoad`|proportion of pre-loading height|`1.3`|`Number`|
|`error`|src of the image upon load fail|`'data-src'`|`String`
|`loading`|src of the image while loading|`'data-src'`|`String`|
|`attempt`|attempts count|`3`|`Number`|
|`listenEvents`|events that you want vue listen for|`['scroll', 'wheel', 'mousewheel', 'resize', 'animationend', 'transitionend', 'touchmove']`| [Desired Listen Events](#desired-listen-events) |
|`adapter`| dynamically modify the attribute of element |`{ }`| [Element Adapter](#element-adapter) |
|`filter`| the image's src filter |`{ }`| [Image url filter](#image-url-filter) |

### Desired Listen Events

You can configure which events you want vue-lazyload by passing in an array
of listener names.

```javascript
Vue.use(VueLazyload, {
  preLoad: 1.3,
  error: 'dist/error.png',
  loading: 'dist/loading.gif',
  attempt: 1,
  // the default is ['scroll', 'wheel', 'mousewheel', 'resize', 'animationend', 'transitionend']
  listenEvents: [ 'scroll' ]
})
```

This is useful if you are having trouble with this plugin resetting itself to loading
when you have certain animations and transitions taking place


### Image url filter

dynamically modify the src of image

```javascript
Vue.use(vueLazy, {
    filter: {
      webp ({ src }) {
          const isCDN = /qiniudn.com/
          if (isCDN.test(src)) {
              src += '?imageView2/2/format/webp'
          }
          return src
      },
      someProcess ({ el, src }) {
        if (el.getAttribute('large')) {
          src += '?large'
        }
        return src
      }
    }
})
```


### Element Adapter

```javascript
Vue.use(vueLazy, {
    adapter: {
        loaded ({ bindType, el, naturalHeight, naturalWidth, $parent, src, loading, error, Init }) {
            // do something here
            // example for call LoadedHandler
            LoadedHandler(el)
        },
        loading (listender, Init) {
            console.log('loading')
        },
        error (listender, Init) {
            console.log('error')
        }
    }
})
```


## Implementation

### Basic

vue-lazyload will set this img element's `src` with `imgUrl` string

```html
<script>
export default {
  data () {
    return {
      imgObj: {
        src: 'http://xx.com/logo.png',
        error: 'http://xx.com/error.png',
        loading: 'http://xx.com/loading-spin.svg'
     }
     imgUrl: 'http://xx.com/logo.png' // String
    }
  }
}
</script>

<template>
  <div ref="container">
     <img v-lazy="imgUrl"/>
     <div v-lazy:background-image="imgUrl"></div>
     
     <!-- with customer error and loading -->
     <img v-lazy="imgObj"/>
     <div v-lazy:background-image="imgObj"></div>
     
     <!-- Customer scrollable element -->
     <img v-lazy.container ="imgUrl"/>
     <div v-lazy:background-image.container="img"></div>
  
    <!-- srcset -->
    <img v-lazy="'img.400px.jpg'" srcset="img.400px.jpg 400w, img.800px.jpg 800w, img.1200px.jpg 1200w">
    <img v-lazy="imgUrl" :srcset="imgUrl' + '?size=400 400w, ' + imgUrl + ' ?size=800 800w, ' + imgUrl +'/1200.jpg 1200w'" />
  </div>
</template>
```

### CSS state

There are three states while img loading

`loading`  `loaded`  `error`

```html
<img src="imgUrl" lazy="loading">
<img src="imgUrl" lazy="loaded">
<img src="imgUrl" lazy="error">
```

```html
<style>
  img[lazy=loading] {
    /*your style here*/
  }
  img[lazy=error] {
    /*your style here*/
  }
  img[lazy=loaded] {
    /*your style here*/
  }
  /*
  or background-image
  */
  .yourclass[lazy=loading] {
    /*your style here*/
  }
  .yourclass[lazy=error] {
    /*your style here*/
  }
  .yourclass[lazy=loaded] {
    /*your style here*/
  }
</style>
```

## Methods

### Event Hook

`vm.$Lazyload.$on(event, callback)`
`vm.$Lazyload.$off(event, callback)`
`vm.$Lazyload.$once(event, callback)`

- `$on` Listen for a custom events `loading`, `loaded`, `error` 
- `$once` Listen for a custom event, but only once. The listener will be removed once it triggers for the first time.
- `$off` Remove event listener(s).

#### `vm.$Lazyload.$on`

#### Arguments:

 * `{string} event`
 * `{Function} callback`

#### Example

```javascript
vm.$Lazyload.$on('loaded', function ({ bindType, el, naturalHeight, naturalWidth, $parent, src, loading, error }) {
  console.log(el, src)
})
```

#### `vm.$Lazyload.$once`

#### Arguments:

 * `{string} event`
 * `{Function} callback`

#### Example

```javascript
vm.$Lazyload.$once('loaded', function ({ el, src }) {
  console.log(el, src)
})
```

#### `vm.$Lazyload.$off`

If only the event is provided, remove all listeners for that event

#### Arguments:

 * `{string} event`
 * `{Function} callback`

#### Example

```javascript
function handler ({ el, src }) {
  console.log(el, src)
} 
vm.$Lazyload.$on('loaded', handler)
vm.$Lazyload.$off('loaded', handler)
vm.$Lazyload.$off('loaded')
```

### LazyLoadHandler

`vm.$Lazyload.lazyLoadHandler`

Manually trigger lazy loading position calculation

#### Example

```javascript

this.$Lazyload.lazyLoadHandler()

```

### Performance

```
this.$Lazyload.$on('loaded', (listener) {
  console.table(this.$Lazyload.performance())
})
```

![performance-demo](http://ww1.sinaimg.cn/large/69402bf8gw1fbo62ocvlaj213k09w78w.jpg)

# Authors && Contributors

- [hilongjw](https://github.com/hilongjw)
- [imcvampire](https://github.com/imcvampire)
- [darrynten](https://github.com/darrynten)
- [biluochun](https://github.com/biluochun)
- [whwnow](https://github.com/whwnow)
- [Leopoldthecoder](https://github.com/Leopoldthecoder)
- [michalbcz](https://github.com/michalbcz)
- [blue0728](https://github.com/blue0728)
- [JounQin](https://github.com/JounQin)
- [llissery](https://github.com/llissery)
- [mega667](https://github.com/mega667)
- [RobinCK](https://github.com/RobinCK)
- [GallenHu](https://github.com/GallenHu)

# License

[The MIT License](http://opensource.org/licenses/MIT)
