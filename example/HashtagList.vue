<template>
  <div class="hashtag-list">
    <div
      class="hashtag-list__item-container" 
      v-for="(item) in hashtagList" 
      :key=item.id
      v-on:click="onSelectItem(item)">

      <!-- content -->
      <div class="hashtag-list__content">
        <span>{{ item.content }}</span>
      </div>
      <div class="hashtag-list__total">
        <span>{{ item.total }}件</span>
      </div>      
    </div>
  </div>
</template>

<style lang="scss" scoped>
$paddingValue: 23px;
$textPadding: 0 $paddingValue;

.hashtag-list {
  width: 100%;
  background: white;
  
  &__item-container {
    width: 100%;
    height: 60px;
    border-bottom: 1px solid #E4E4E4;
    display: grid;
    grid-template-columns: 1fr 100px;
  
    // NOTE: Set vertical alignment center
    display: flex;      
    align-items: center;
  }

  &__content {
    height: 20px;
    padding: $textPadding;
    overflow: hidden;
    width: 80%;
    text-overflow: ellipsis;

    // NOTE: Do not allow line break
    white-space: nowrap;
  }

  &__total {
    width: 20%;
    font-size: 13px;    
    color: #B5B5B5;
    text-align: right;
    padding-right: $paddingValue; 
  }
}
</style>

<script>
export default {
  props: {
    hashtagList: {
      type: Array,
      default: _ => ([])
    }
  },
  data() {
    return {
      stlyeObject: {
        height: '150px'
      }
    }
  },
  methods: {
    onSelectItem(item) {
      this.$emit('onSelectItem', item)
    }
  }
}
</script>
