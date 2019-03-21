# Highlight hashtags on Vue.js module

## Features
* Highlight hastags in textarea
* Resize textarea automatically
* Notify inputting hashtag in realtime
* Support japanese.

## Getting started
### Download
Via npm

```
$ npm install vue-hashtag-textarea --save
```

### Usage
### ES6 modules
```
import VueHashtagTextarea from 'vue-hashtag-textarea';
```

### Vue file
```
import VueHashtagTextarea from '/path/to/vue-hashtag-textarea.vue';
```

## Hello world
**NOTE:**

```js
<template>
  <div class="container">
    <vue-hashtag-textarea :option=option v-on:onChangedHashtag="onChangedHashtag" />
  <div>  
</template>

<script>
  import VueHashtagTextarea from 'vue-hashtag-textarea'
  export default {
    data() {
      return {
        option: {
          textColor:'#ff0000'
        }
      }
    },
    components: {
      VueHashtagTextarea,
    },
    mounted() {},
    methods: {
      onChangedHashtag(info) {
        // NOTE: Everytime hashtags is inputed in vue-hashtag-textarea,
        //       values will be callbacked in this scope
      }
    },
  }
</script>
```

## Options
|options|description|default|
|:--|:--|:--|
|textColor|ordinary text color|black|
|font|wave height|14px "Noto Sans Japanese", sans-serif|
|hashtagBackgroundColor|background color on hashtag|transparent|
|hashtagColor|hashtag color|#ff0000|
|placeholder|placeholder on empty|Sentence for placeholder #place #holder|

## Callback Function
### **onChangedHashtag(obj)**
#### Argument
obj : Object
|values|type|description|
|:--|:--|:--|
|target|String|focusing hashtag|
|hashtags|Array|hashtag list in the sentence|

#### Description
Everytime hashtags is inputed in vue-hashtag-textarea,
values including whole hashtags and current focusing hashtag will be notified by this callback function. If caret is not at hashtag, this callback doesn't be fired.

## Browser support
| Chrome | Safari | IE / Edge | Firefox | Opera |
| --- | --- | --- | --- | --- |
| 72+ | 12+ | non-supported | 65+ | non-supported |

#### NOTE
If debugging iOS devices on chrome dev tools, the layout will be failed. In that case, recommend to use Safari browser instead.


## License
**MIT**