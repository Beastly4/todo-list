<template>
  <header class="header">
    <div class="mainer">
      <div class="logo">
        Todo list
        <span v-if="list.length">({{list.length}})</span>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  props: ["list"],
  data() {
    return {};
  }
};
</script>

<style lang="scss" scoped>
@import "./../styles/_vars.scss";

.header {
  background-color: $colorBase;
  padding: $space 0;
  margin: 0 0 ($space * 2) 0;

  .logo {
    color: #fff;
    text-transform: uppercase;
    font-weight: bold;
  }
}
</style>