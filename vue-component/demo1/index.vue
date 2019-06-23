<template>
  <div class='slide-box'>
    <div class='slide-img-block'>
      <!-- 图片 -->
      <img
        class='slide-img'
        id='slideImg'
        src=''
      />
      <!-- 裁剪出来的拼图 -->
      <div
        class='slide-block'
        id='slideBlock'
      ></div>
      <!-- 裁剪区域 -->
      <div
        class='slide-cut-block'
        id='cutBlock'
      ></div>
    </div>
    <div class='scroll-bar'>
      <div
        class='slide-btn'
        id='slideBtn'
        draggable="true"
        @touchstart="touchstart($event)"
      ></div>
      <div
        class='slide-title'
        id='slideHint'
      > 按住滑块，拖动完成上面拼图</div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'SliderVerification',
  components: {},
  directives: {},
  filters: {},
  mixins: [],
  props: {},
  data() {
    return {
      slideBtn: null, // 拖拽按钮
      slideImg: null, // 图片
      slideBlock: null, // 裁剪出来的拼图
      cutBlock: null, // 裁剪区域
      resultX: 0,
      imgWidth: 0,
      imgHeight: 0,
      imgList: [
        { url: require('@/assets/images/slider/1.jpg') },
        { url: require('@/assets/images/slider/2.jpg') },
        { url: require('@/assets/images/slider/3.jpg') },
        { url: require('@/assets/images/slider/4.jpg') },
        { url: require('@/assets/images/slider/5.jpg') },
        { url: require('@/assets/images/slider/6.jpg') },
        { url: require('@/assets/images/slider/7.jpg') },
        { url: require('@/assets/images/slider/8.jpg') },
        { url: require('@/assets/images/slider/9.jpg') }
      ],
      isSuccess: true
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {
    this.$nextTick(function() {
      for (var i = 1; i < 10; i++) {
        this.imgList.push(i + '.jpg');
      }
      this.slideImg = document.getElementById('slideImg');
      this.slideBlock = document.getElementById('slideBlock');
      this.cutBlock = document.getElementById('cutBlock');
      this.slideBtn = document.getElementById('slideBtn');
      this.setImg();
    });
  },
  methods: {
    setImg() {
      // 该方法有等于0 的情况
      var index = Math.round(Math.random() * 8);
      // 获取随机图片
      var imgSrc = this.imgList[index].url;
      // 设置图片
      this.slideImg.setAttribute('src', imgSrc);
      // 设置拼图背景图
      this.slideBlock.style.backgroundImage = 'url(' + imgSrc + ')';
      // 图片加载完后获取图片宽/高
      this.slideImg.onload = e => {
        e.stopPropagation();
        this.imgWidth = this.slideImg.offsetWidth;
        this.imgHeight = this.slideImg.offsetHeight;
      };
    },
    cutImg() {
      var that = this;
      that.cutBlock.style.display = 'block';
      // 裁剪区域宽
      var cutWidth = that.cutBlock.offsetWidth;
      // 裁剪区域高
      var cutHeight = that.cutBlock.offsetHeight;
      // left
      that.resultX = Math.floor(
        Math.random() * (that.imgWidth - cutWidth * 2 - 20) + cutWidth
      );
      // top
      var cutTop = Math.floor(
        Math.random() * (that.imgHeight - cutHeight * 2) + cutHeight
      );
      // 设置样式
      that.cutBlock.style.cssText = `top:${cutTop}px;left:${
        that.resultX
      }px; display: block;`;
      that.slideBlock.style.top = `${cutTop}px`;
      that.slideBlock.style.backgroundSize = `${that.imgWidth}px ${
        that.imgHeight
      }px`;
      that.slideBlock.style.backgroundPosition = `-${
        that.resultX
      }px -${cutTop}px`;

      that.slideBlock.style.opacity = '1';
    },
    touchstart(e) {
      let that = this;
      e.preventDefault();
      let startX = e.touches[0].pageX;
      var target = e.target;
      if (this.isSuccess) {
        this.cutImg();
      }
      target.addEventListener('touchmove', touchmove);
      target.addEventListener('touchend', touchend);

      // 拖拽
      function touchmove(event) {
        // 滑动按钮允许最远滑动距离
        let maxWidth = that.imgWidth - that.cutBlock.offsetWidth;
        // 滑动距离
        let x = event.touches[0].pageX - startX;
        if (x < 0) {
          that.slideBtn.style.left = '0px';
          that.slideBlock.style.left = '20px';
        } else if (x >= 0 && x <= maxWidth) {
          that.slideBtn.style.left = x + 'px';
          that.slideBlock.style.left = x + 'px';
        } else {
          that.slideBtn.style.left = maxWidth + 'px';
          that.slideBlock.style.left = maxWidth + 'px';
        }
        that.slideBtn.style.transition = 'none';
        that.slideBlock.style.transition = 'none';
      }
      // 放开
      function touchend() {
        var left = that.slideBlock.style.left;
        left = parseInt(left.substring(0, left.length - 2));
        // 比较拼图left和裁剪区域left，允许5px的误差
        if (left > that.resultX - 5 && left < that.resultX + 5) {
          that.isSuccess = true;
          // 拼图
          that.slideBlock.style.opacity = '0';
          that.slideBlock.style.transition = 'opacity 0.6s';
          // 裁剪区域
          that.cutBlock.style.opacity = '0';
          that.cutBlock.style.transition = 'opacity 0.6s';
          setTimeout(function() {
            that.cutBlock.style.display = 'none';
            that.slideBlock.style.left = '20px';
            that.setImg();
          }, 600);
        } else {
          that.isSuccess = false;
          // 拼图
          that.slideBlock.style.left = '20px';
          that.slideBlock.style.transition = 'left 0.6s';
        }
        that.slideBtn.style.left = '0';
        that.slideBtn.style.transition = 'left 0.6s';
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.slide-img-block {
  position: relative;
  width: 100%;
  height: 100%;
}
.slide-img-block img {
  width: 100%;
  border-radius: 10px;
}

.slide-block {
  opacity: 0;
  position: absolute;
  top: 0;
  left: 20px;
  width: 40px;
  height: 40px;
  border-radius: 4px;
  background-repeat: no-repeat;
  background-attachment: scroll;
  border: 1px solid rgba(255, 255, 0, 0.8);
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.4), 0 0 10px 0 rgba(90, 90, 90, 0.4);
  box-sizing: border-box;
  z-index: 10;
}
.slide-cut-block {
  display: none;
  position: absolute;
  width: 40px;
  height: 40px;
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.2);
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.5) inset;
}
.scroll-bar {
  position: relative;
  width: 100%;
  height: 20px;
  margin: 10px 0;
  background-color: #ececec;
  border-radius:10px;
}

.slide-btn {
  position: absolute;
  left: 0;
  top: -7px;
  height: 34px;
  width: 34px;
  background-color: blue;
  box-shadow: none;
  -moz-box-shadow: none;
  border-radius: 13px;
  z-index: 399;
}

.slide-title {
  position: absolute;
  left: 35px;
  width: 220px;
  height: 20px;
  line-height: 20px !important;
  text-align: center;
  font-size: 12px !important;
  color: #486c80;
  opacity: 1;
  filter: alpha(opacity=100);
  user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  -webkit-user-select: none;
}
</style>

