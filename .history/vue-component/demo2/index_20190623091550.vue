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
      >
        <img :src="slideBlockImg">
      </div>
      <!-- 裁剪区域 -->
      <div
        class='slide-cut-block'
        id='cutBlock'
      >
        <img :src="slideBlockImg">
      </div>
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
  props: {
    slideImg: {
      value: ''
    },
    // 拼图的那一块
    slideBlockImg: {
      value: ''
    },
    resultX: {
      value: 0
    },
    resultY: {
      value: 0
    }
  },
  data() {
    return {
      slideBtn: null, // 拖拽按钮
      slideImgBlock: null, // 图片
      slideBlock: null, // 裁剪出来的拼图
      cutBlock: null, // 裁剪区域
      imgWidth: 0,
      imgHeight: 0,
      isSuccess: true
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {
    this.$nextTick(function() {
      this.slideImgBlock = document.getElementById('slideImg');
      this.slideBlock = document.getElementById('slideBlock');
      this.cutBlock = document.getElementById('cutBlock');
      this.slideBtn = document.getElementById('slideBtn');
      this.setImg();
    });
  },
  methods: {
    setImg() {
      // 设置图片
      this.slideImgBlock.setAttribute('src', this.slideImg);
      // 图片加载完后获取图片宽/高
      this.slideImgBlock.onload = e => {
        e.stopPropagation();
        this.imgWidth = this.slideImgBlock.offsetWidth;
        this.imgHeight = this.slideImgBlock.offsetHeight;
      };
    },
    cutImg() {
      var that = this;
      that.cutBlock.style.display = 'block';
      // 设置样式
      that.cutBlock.style.cssText = `top:${this.resultY}px;left:${
        that.resultX
      }px; display: block;`;
      that.slideBlock.style.top = `${this.resultY}px`;
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
          that.$emit('slider-success', true);
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
  box-sizing: border-box;
  z-index: 10;
  img {
    width: 40px;
    height: 40px;
  }
}
.slide-cut-block {
  display: none;
  position: absolute;
  width: 40px;
  height: 40px;
  border-radius: 4px;
  img {
    width: 40px;
    height: 40px;
    -webkit-filter: brightness(0.30);
    filter: brightness(0.30);
  }
}
.scroll-bar {
  position: relative;
  width: 100%;
  height: 20px;
  margin: 10px 0;
  background-color: #ececec;
  border-radius: 10px;
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

