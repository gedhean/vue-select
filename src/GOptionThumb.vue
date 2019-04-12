<template>
  <div class="g-thumb-option">
    <span class="g-thumb-body">
        <span class="g-thumb-img thumb" :style="{'background-image': `url(${imageSrc})`}"></span>
        <span class="g-thumb-label">{{ label }}</span>
    </span>
  </div>
</template>
<script>
export default {
  props: {
    imageSrc: {
      type: String,
      default: () => "https://static-production.gocase.com.br/case_types/16/original/colorcaseblack.jpg?1508519603",
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
 .thumb {
   width: 30px;
   height: 30px;
   /* border-radius: 50%; */
   display: inline-block;
 }

 .g-thumb-option {
   cursor: pointer;
 }
</style>
