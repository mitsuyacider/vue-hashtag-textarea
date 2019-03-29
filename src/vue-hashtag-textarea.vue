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
  $textWidth: 100%;
  $textPadding: 0 23px;

  // NOTE: Component of hashtag textarea
  .hashtag-textarea {
    position: relative;
    overflow: auto;
    border-bottom: 1px solid #E4E4E4;
    background: white;
    min-height: 120px;
    height: 100%;
    width: 100%;

    // NOTE: Textarea layout. Fix position
    &__true-text {
      border: 0;
      background: transparent;
      position: absolute;
      top: 0;
      color: transparent;
      caret-color: black;
      outline: none;
    }    

    &__overlay {
      -webkit-tap-highlight-color: transparent; 
    }  

    &__overlay, &__true-text {
      padding: $textPadding;
      margin: 0px;
      height: 100%;
      width: $textWidth;
      white-space: pre-wrap;
      overflow-wrap: break-word;
    }

    &__placeholder {
      font: 14px "HiraKakuProN-W4-AlphaNum";
      padding: $textPadding;
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
      type: Object
    }
  },
  data() {
    return {
      hashtagList: [],
      shouldShowPlaceholder: true,
      defaultOption: {
          defaultContent: '',
          textColor: 'black',
          font: '14px "HiraKakuProN-W4-AlphaNum"',
          hashtagBackgroundColor: 'transparent',
          hashtagColor: '#ff0000',
          placeholder: 'Sentence for placeholder #place #holder',
          isEditMode: true
      },
      focusedHashtagNode: {}
    }
  },
  created() {    
    // NOTE: Set default option if not existence in props
    for(let key in this.defaultOption) {
      const value = this.option[key]
      if (value === undefined) {
        this.option[key] = this.defaultOption[key]
      }
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

    // NOTE: Display initial content if exist
    target.innerText = this.option.defaultContent

    const overlayElm = document.getElementById('input-overlay')
    overlayElm.addEventListener("click", this.onSelectHashtag, false);
  },
  destroyed() {
    const overlayElm = document.getElementById('input-overlay')
    overlayElm.removeEventListener("click", this.onSelectHashtag, false);
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
      style['z-index'] = this.option.isEditMode ?  2 : -2;
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

      if (!this.option.isEditMode) {
        style += 'cursor: default;'
      }
      style += '"'

      return style
    },
    regExp: function() {
      return /\#(?!\,)[\S]+/g;
      
    }
  },
  methods: {
    onObserveElement(mutations) {
      mutations.forEach((mutation) => {
        const type = mutation.type

        switch(type) {
          // NOTE: The textContent will be changed in the line, this type is called.
          case 'characterData':
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

      // NOTE: Need to convert html character with escapecharacter
      const content = this.escapeHtml(target.innerText)
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
        const idStr = ' id=' + self.getUniqueStr()
        const result = '<i ' + self.hashtagStyle + idStr + '>' + match + '</i>'
        return result
      })
      
      // NOTE: Apply content into overlay field.
      const insertNode = document.getElementById('input-overlay')
      insertNode.innerHTML = replaceContent

      this.hashtagList = this.getHashtagList(content)      
    },
    escapeHtml(content) {
      const escapeHashMap = {
        "&": "&amp;",
        "\"": "&quot;",
        "<": "&lt;",
        ">": "&gt;"
      };
      return content.replace(/[&"<>]/g, function(match) {
        return escapeHashMap[match];
      });
    },    
    getHashtagList(value) {
      const result = value.match(this.regExp)
      return result;
    },
    getHashtagNodeList() {
      const target = document.getElementById('input-overlay')
      const nodes = []      
      const childNodes = target.childNodes
      childNodes.forEach((child) => {
        nodes.push(child)
      })

      return nodes
    },
    getDiffArrayWithIndex(newVal, oldVal) {
      const result = {}
      
      for(let i = 0 ; i < newVal.length; i++){
        if(oldVal.indexOf(newVal[i]) === -1 ){
          result.diffValue = newVal[i]
          result.index = i
          break
        }    
      }

      return result
    },
    onSelectHashtag(e) {
      const target = e.target
      const tagName = target.tagName
      if (tagName === 'I') {
        const content = target.textContent
        this.$emit('onSelectHashtag', target)
      }
    },
    replaceHashtagNodeContent(newValue) {
      this.focusedHashtagNode.textContent = newValue
      const trueTarget = document.getElementById('input-true-text');
      const overlayTarget = document.getElementById('input-overlay');
      trueTarget.innerText = overlayTarget.innerText
    },
    getUniqueStr(){
      var strong = 1000;
      return new Date().getTime().toString(16)  + Math.floor(strong*Math.random()).toString(16)
    },
    getHashtagTargetNode(index) {
      const target = document.getElementById('input-overlay')
      const nodes = target.childNodes
      let count = 0
      let hashtagTarget = {}

      for (let i = 0; i < nodes.length; i++) {
        const node = nodes[i]
        if (node.tagName === 'I') {
          if (index === count) {
            hashtagTarget = node
            break
          }

          count++
        }
      }
      return hashtagTarget
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
          // NOTE: Set focused hashtag node
          const hashtagTarget = this.getHashtagTargetNode(0) 
          hashtagData.target = hashtagTarget.innerText
          this.focusedHashtagNode = hashtagTarget
        } else {          
          if (newVal.length < oldVal.length) {
            hashtagData.target = ''
          } else {
            // NOTE: Set focused hashtag node
            const diff = this.getDiffArrayWithIndex(newVal, oldVal)
            const index = diff.diffValue === undefined ? newVal.length - 1 : diff.index
            const hashtagTarget = this.getHashtagTargetNode(index)
            hashtagData.target = hashtagTarget.innerText
            this.focusedHashtagNode = hashtagTarget
          }
        }
        
        hashtagData.hashtags = newVal
        this.$emit('onChangeHashtag', hashtagData)
      }    
    }
  }
}
</script>