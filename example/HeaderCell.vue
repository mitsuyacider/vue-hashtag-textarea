<template>
  <div class="container">
    <header>
      <slot></slot>
    </header>      
  </div>
</template>

<style lang="scss" scoped>
$textPadding: 0 23px;

.container {
  width: 100%;
  height: 34px;
  display: table;
  background: white;

  header {
    display: table-cell;
    vertical-align: middle;
    padding: $textPadding;
    // font: 14px "SF-Pro-Text-Bold";
    font-size: 14px;
    font-weight: bold;
  }
}
</style>
