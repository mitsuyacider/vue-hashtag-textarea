<template>
  <div id="input" class="hashtag-textarea">
    <div 
      id="input-overlay"
      class="hashtag-textarea__overlay"
      :style=overlayStyle />
    <div 
      id="input-true-text"
      class="hashtag-textarea__true-text"
      wrap="soft"
      :style=trueTextStyle
      contentEditable="true"
      spellcheck="false">
    </div>

    <div class="hashtag-textarea__placeholder" v-show="shouldShowPlaceholder">
      {{ option.placeholder }}
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
      color: transparent;
      caret-color: black;
      outline: none;
    }    

    &__overlay {
      color: black;
      
      // NOTE: Hashtag layout
      .hashtag {
        background: #fff000; 
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

<script>
export default {
  name: 'VueHashtagTextarea',
  props: {
    option: {
      type: Object,
      default: _ => (
        {
          textColor: 'black',
          font: '14px "Noto Sans Japanese", sans-serif',
          hashtagBackgroundColor: 'transparent',
          hashtagColor: '#ff0000',
          placeholder: 'Sentence for placeholder #place #holder'
        }
      )
    }
  },
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
  computed: {
    isSafariBrowser: function() {
      let isSafariBrowser = false
      const userAgent = window.navigator.userAgent.toLowerCase();
      
      if(userAgent.indexOf('safari') !== -1 &&
        userAgent.indexOf('chrome') === -1 ) {
        isSafariBrowser = true
      }
      
      return isSafariBrowser;
    },
    trueTextStyle: function() {
      let style = {}
      style.font = this.option.font
      
      return style
    },
    overlayStyle: function() {
      let style = {}
      style.color = this.option.textColor
      style.font = this.option.font

      return style
    },
    hashtagStyle: function() {
      let style = 'style="'
      style += 'color:' + this.option.hashtagColor + ';'
      style += 'background:' + this.option.hashtagBackgroundColor + ';'
      style += '"'

      return style
    },
    regExp: function() {
      return /#[Ａ-Ｚａ-ｚA-Za-z一-龥0-9０-９ぁ-ヶｦ-ﾟー]+/g;
    }
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
      const srcContent = this.isSafariBrowser ? content : content2
      const self = this
      const replaceContent = srcContent.replace(this.regExp, function(match) {
        const result = '<i ' + self.hashtagStyle + '>' + match + '</i>'        
        return result
      })
      
      // NOTE: Apply content into overlay field.
      const insertNode = document.getElementById('input-overlay')
      insertNode.innerHTML = replaceContent

      this.hashtagList = this.getHashtagList(content)      
    },
    getHashtagList(value) {
      const result = value.match(this.regExp)
      return result;
    },
    diffArray(newVal, oldVal) {
      return newVal.concat(oldVal)
        .filter(item => !oldVal.includes(item));
    }    
  },
  watch: {
    hashtagList: function(newVal, oldVal) {

      const isEqualVal = JSON.stringify(newVal) === JSON.stringify(oldVal)
      if (!isEqualVal) {
        const hashtagData = {}
        if (newVal === null) {
          hashtagData.target = ''
        } else if (oldVal === null) {
          hashtagData.target = newVal[0]
        } else {
          const diff = this.diffArray(newVal, oldVal)
          hashtagData.target = diff.length === 0 ? newVal[newVal.length - 1] : diff[0]
        }

        hashtagData.hashtags = newVal
        this.$emit('onChangedHashtag', hashtagData)        
      }    
    }
  }
}
</script>