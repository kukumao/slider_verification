<template>
  <div>
    <slider-verification
      ref="sliderVerification"
      @slider-success="sliderSuccess"
      :slideImg="slideImg"
      :slideBlockImg="slideBlockImg"
      :resultX="resultX"
      :resultY="resultY"
    ></slider-verification>
  </div>
</template>

<script>
export default {
  name: "Login",
  components: {},
  directives: {},
  filters: {},
  mixins: [],
  props: {},
  data() {
    return {
      // 背景图片
      slideImg:
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1561025801750&di=a97011cec393d5ac0dc6f88faf57670b&imgtype=0&src=http%3A%2F%2Fpic37.nipic.com%2F20140113%2F8800276_184927469000_2.png",
      // 截取拼图图片
      slideBlockImg: "https://timgsa.baidu.com/timg?pt.png",
      resultX: 200,
      resultY: 50
    };
  },
  computed: {
    this.$refs.sliderVerification.setImg();
  },
  watch: {},
  created() {},
  mounted() {},
  methods: {}
};
</script>

<style lang="scss" scoped>
</style>
