<template>
  <section class="container">
    <header-cell>Description</header-cell>
    <vue-hashtag-textarea 
      :option=option 
      v-on:onChangeHashtag="onChangeHashtag" 
      v-on:onSelectHashtag="onSelectHashtag"
      ref="vueHashtagTextarea" />
    <hashtag-list 
      :hashtagList=hashtagList
      v-on:onSelectItem="onSelectItem" />
  </section>
</template>

<style lang="scss" scoped>

</style>

<script>
import VueHashtagTextarea from '../src/vue-hashtag-textarea'
import HashtagList from './HashtagList'
import HeaderCell from './HeaderCell'

export default {
  data() {
    return {
      hashtagList: [
        {
          content: '#today',
          total: 145,
          id: 0
        },
        {
          content: '#tomorrow',
          total: 145,
          id: 1
        },
        {
          content: '#今日',
          total: 145,
          id: 2
        },
        {
          content: '#明日',
          total: 145,
          id: 3
        }
      ],
      option: {
        placeholder: 'share your great topic. #topic #experience'
      }
    }
  },
    methods: {
      onChangeHashtag(info) {   
        console.log(info)   
      },
      onSelectHashtag(target) {
        console.log(target)      
      },
      onSelectItem(item) {      
        this.$refs.vueHashtagTextarea.replaceHashtagNodeContent(item.content + ' ')
      }
    },
  components: {
    VueHashtagTextarea,
    HashtagList,
    HeaderCell
  },  
}
</script>
