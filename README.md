# Highlight hashtags on Vue.js module

[![npm version](https://img.shields.io/npm/v/vue-hashtag-textarea.svg?style=flat&color=blue)](https://www.npmjs.com/package/vue-hashtag-textarea)
[![npm download](https://img.shields.io/npm/dm/vue-hashtag-textarea.svg?style=flat)](https://www.npmjs.com/package/vue-hashtag-textarea)

## Features
* Highlight hastags in textarea
* Resize textarea automatically
* Notify inputting hashtag in realtime
* Support japanese.

| Desktop | Mobile | 
| --- | --- | 
| ![desktop](./desktop.gif)| ![mobile](./mobile.gif) |



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
```js
<template>
  <div class="container">
    <vue-hashtag-textarea />
  <div>  
</template>

<script>
  import VueHashtagTextarea from 'vue-hashtag-textarea'
  export default {
    components: {
      VueHashtagTextarea,
    }
  }
</script>
```

## Options
| Options | Type | Description | Default |
|:--|:--|:--|:--|
| defaultContent | String | insert initial text | '' |
| textColor | String | ordinary text color| black |
| font | String | wave height | 14px "Noto Sans Japanese", sans-serif |
| hashtagBackgroundColor | String | background color on hashtag| transparent |
| hashtagColor | String | hashtag color | #ff0000 |
| placeholder |  String | placeholder on empty | Sentence for placeholder #place #holder |
| isEditMode |  Boolean | **true:** enable to edit but cannot select hashtag<br>**false:** enable to select hashtag but cannot edit | true |

## Supported Functions
### **replaceHashtagNodeContent(content)**

##### Argument
* content : String

##### Description
Replace focused hashtag to new hashtag. Please note that the content shouled include hashtag "#" at the head of the word. In addition, if the caret in the textarea is not on one of hashtags, no hashtag is replaced.

##### Example

```js
<template>
  <div class="container">
    <vue-hashtag-textarea
      ref="vueHashtagTextarea" />
  <div>  
</template>

<script>
  import VueHashtagTextarea from 'vue-hashtag-textarea'
  export default {
    components: {
      VueHashtagTextarea,
    },
    methods: {
      someEventTrigger() {
        this.$refs.vueHashtagTextarea.replaceHashtagNodeContent('#somehashtag ')
      }
    }
  }
</script>
```

## Callback

### **onChangeHashtag(obj)**

##### Argument

* obj : Object

| Values | Type | Description |
|:--|:--|:--|
|target|String|focusing hashtag|
|hashtags|Array|hashtag list in the sentence|

##### Description
Everytime hashtags is inputed in vue-hashtag-textarea,
values including whole hashtags and current focusing hashtag will be notified by this callback function. If caret is not at hashtag, this callback doesn't be fired.

##### Example
```js
<template>
  <div class="container">
    <vue-hashtag-textarea
      v-on:onChangeHashtag="onChangeHashtag" />
  <div>  
</template>

<script>
  import VueHashtagTextarea from 'vue-hashtag-textarea'
  export default {
    methods: {
      onChangeHashtag(obj) {
        // NOTE: Everytime hashtags is inputed in vue-hashtag-textarea,
        //       values will be callbacked in this scope
      }
    }
  }
</script>
```

### **onSelectHashtag(content)**

##### Argument

* content : String

##### Description
When hashtag element is selected, this callback function will be fired with the specific hashtag.

##### Example
```js
<template>
  <div class="container">
    <vue-hashtag-textarea
      :option=option
      v-on:onSelectHashtag="onSelectHashtag" />
  <div>  
</template>

<script>
  import VueHashtagTextarea from 'vue-hashtag-textarea'
  export default {
    data() {
      return {
        option: {
          isEditMode: false
        }
      }
    },
    components: {
      VueHashtagTextarea,
    },
    methods: {
      onSelecthashtag(content) {
        // TODO:
      }
    }
  }
</script>
```


## Browser support

### Desktop
| Chrome | Safari | IE / Edge | Firefox | Opera |
| --- | --- | --- | --- | --- |
| 72+ | 12+ | ? | 65+ | ? |

### Mobile
| Android webview | Chrome for Android | Safari on iOS | Edge Mobile | Firefox for Android | Opera for Android | Samsung Internet |
| --- | --- | --- | --- | --- | --- | --- |
| ? | ? | 12+ | ? | ? | ? | ? |

##### NOTE
<small>If debugging iOS devices on chrome dev tools, the layout will be failed. In that case, recommend to use Safari browser instead.</small>

## Quick Start
```
git clone https://github.com/mitsuyacider/vue-hashtag-textarea
cd vue-hashtag-textarea
npm install
vue serve example/App.vue
```

## License
**[MIT](https://github.com/mitsuyacider/vue-hashtag-textarea/blob/master/LICENSE.txt)**