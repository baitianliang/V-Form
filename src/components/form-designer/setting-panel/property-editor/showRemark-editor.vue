<template>
  <el-form-item :label="i18nt('designer.setting.remark')">
    <el-input type="text" v-model="optionModel.remark"></el-input>
  </el-form-item>
</template>

<script>
  import i18n from "@/utils/i18n"

  export default {
    name: "showRemark-editor",
    mixins: [i18n],
    props: {
      designer: Object,
      selectedWidget: Object,
      optionModel: Object,
    },
  }
</script>

<style scoped>

</style>
