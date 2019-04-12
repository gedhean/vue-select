<template>
  <div class="g-bullet-option">
    <span class="g-bullet-body">
        <span class="g-bullet-img bullet" :style="{'background-color': color}"></span>
        <span class="g-bullet-label">{{ label }}</span>
    </span>
  </div>
</template>
<script>
export default {
  props: {
    color: {
      type: String,
      default: () => "",
      required: false
    },
    label: {
      type: String,
      default: () => "",
      required: false
    }
  }
}
</script>
<style scoped>
 .bullet {
   width: 30px;
   height: 30px;
   border-radius: 50%;
   display: inline-block;
 }

 .g-bullet-option {
   cursor: pointer;
 }
</style>
