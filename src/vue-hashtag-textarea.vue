<script>
export default {
  name: 'VueHashtagTextarea',
  data() {
    return {
      hashtagList: [],
      shouldShowPlaceholder: true
    }
  },
  mounted() {
    // NOTE: Create Observer. This is called when text content of the target is changed
    const target = document.getElementById('input-true-text');
    const observer = new MutationObserver(this.onObserveElement);    
    const config = { 
                    childList: true, 
                    characterData: true,
                    characterDataOldValue: true,
                    subtree: true
                  };
    
    observer.observe(target, config);
  },
  methods: {
    onObserveElement(mutations) {
      mutations.forEach((mutation) => {
        const type = mutation.type

        switch(type) {
          // NOTE: The textContent will be changed in the line, this type is called.
          case 'characterData':
            const target = document.getElementById('input-true-text');    
            const content = target.value
            this.replaceContent()
            break;

          // NOTE: The line break will be occured, this type is called.
          case 'childList':
            this.replaceContent()
            break;
          
          default:
            break;        
        }
      })

      // NOTE: Show / hide placeholder.
      const target = document.getElementById('input-true-text');
      this.shouldShowPlaceholder = target.innerHTML === '' || 
                                    target.innerHTML === '<br>'      
    },
    replaceContent() {
      const target = document.getElementById('input-true-text');    
      const content = target.innerText
      const contentHTML = target.textContent
      
      // NOTE: Trim line break \n
      const spaceExp = /^\n\n/gm
      const content2 = content.replace(spaceExp, function(match) {
        return '\n'
      })

      // NOTE: Create new text for hashtag highlight to insert element
      //       If debugging on chrome, use content2 above, otherwise
      //       line break point will be failed.
      const exp = /#[Ａ-Ｚａ-ｚA-Za-z一-龥0-9０-９ぁ-ヶｦ-ﾟー]+/g;
      const replaceContent = content.replace(exp, function(match) {
        const result = '<i class="hashtag">' + match + '</i>'        
        return result
      })
      
      // NOTE: Apply content into overlay field.
      const insertNode = document.getElementById('input-overlay')
      insertNode.innerHTML = replaceContent

      this.hashtagList = this.getHashtagList(content)      
    },
    getHashtagList(value) {
      const exp = /#[Ａ-Ｚａ-ｚA-Za-z一-龥0-9０-９ぁ-ヶｦ-ﾟー]+/g;
      // const result = exp.test(value);
      const result = value.match(exp)
      return result;
    }    
  },
  watch: {
    hashtagList: function(newVal, oldVal) {
      if (newVal !== oldVal) {
        this.$emit('onChangedHashtagList', newVal)        
      }    
    }
  }
}
</script>

<template>
  <div id="input" class="hashtag-textarea">
    <div 
      id="input-overlay"
      class="hashtag-textarea__overlay" />
    <div 
      id="input-true-text"
      class="hashtag-textarea__true-text"
      placeholder="説明を入力する#テキスト#テキスト"
      wrap="soft"
      contentEditable="true"
      spellcheck="false">
    </div>

    <div class="hashtag-textarea__placeholder" v-show="shouldShowPlaceholder">
      This is placeholder #place #holder
    </div>
  </div>
</template>

<style lang="scss">
  // TODO: Get proper font. Should load this from local.
  @import url(http://fonts.googleapis.com/earlyaccess/notosansjapanese.css);
  $myFont: 14px 'Noto Sans Japanese', sans-serif;
  $textWidth: 100%;

  // NOTE: Component of hashtag textarea
  .hashtag-textarea {
    position: relative;
    overflow: auto;
    border: 1px solid #ccc;
    background: #f9f9f9;
    min-height: 150px;
    height: 100%;

    // NOTE: Textarea layout. Fix position
    &__true-text {
      z-index: 2;
      border: 0;
      background: transparent;
      position: absolute;
      top: 0;
      // color: transparent;
      caret-color: red;
      outline: none;
    }    

    &__overlay {
      // color: black;
      color:red;
      
      // NOTE: Hashtag layout
      .hashtag {
        background: #fff000; 
        color: red;
        font: $myFont;
      }        
    }  

    &__overlay, &__true-text {
      padding: 10px;
      margin: 0px;
      font: $myFont;
      height: 100%;
      width: $textWidth;
      white-space: pre-wrap;
      overflow-wrap: break-word;
    }

    &__placeholder {
      padding: 10px;
      position: absolute;
      top: 0;
      color: lightgray;
    }
  }
</style>
