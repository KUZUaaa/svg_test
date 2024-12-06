<template>
  <svg width="100%" height="97vh" @pointerup="stop" @pointermove="move">
    <circle cx="900" cy="400" :r="30 * (Math.random() + 0.5)" :fill="'hsl('+i*30+',50%,50%)'" @pointerdown="down" v-for="i in 10" :key="i"
      :id="i">
    </circle>
    <!-- 矩形 -->
  </svg>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isActive: false,
      activeId: -1,
      start: { x: 0, y: 0 },  // 用于记录起始位置
      def:'',//选中的原
    };
  },
  mounted(){
    this.init()
  },
  methods: {
    // 初始化每个圆
    init(){
        const centerX = 900;
        const centerY = 400;
        let doms = document.querySelectorAll(`circle:not([id="${this.activeId}"])`);
        doms = Array.from(doms)
        doms.forEach(dom => {
          let x = dom.getAttribute('cx')
          let y = dom.getAttribute('cy')
          if (x != centerX || y != centerY) {
            dom.setAttribute('cx', +x + 0.02 * (centerX - x))
            dom.setAttribute('cy', +y + 0.02 * (centerY - y))
            this.pg(dom)
          }
        })
        
      requestAnimationFrame(this.init)
    },
    // 当鼠标按下时，记录起始位置并激活拖动
    down(e) {
      const circle = e.target;
      this.def = circle
      const rect = circle.getBoundingClientRect();
      this.start.x = e.clientX - rect.left;  // 计算鼠标相对于圆心的偏移
      this.start.y = e.clientY - rect.top;
      this.isActive = true;
      this.activeId = circle.id;
    },

    // 拖动过程中，更新圆形的位置
    move(e) {
      if (!this.isActive) return;
      
      const circle = this.def;
      const rect = circle.getBoundingClientRect();

      // 计算新的圆心位置
      const dx = e.clientX - rect.left - this.start.x;
      const dy = e.clientY - rect.top - this.start.y;

      // 更新圆形的 cx 和 cy 属性
      const newCx = parseFloat(circle.getAttribute('cx')) + dx;
      const newCy = parseFloat(circle.getAttribute('cy')) + dy;
      circle.setAttribute('cx', newCx);
      circle.setAttribute('cy', newCy);
      this.pg(this.def)
      // // 获取所有其他的圆形
      // const alldoms = Array.from(e.target.parentNode.querySelectorAll('circle:not([id="' + this.def.id + '"])'));

      // // 检查每个圆形，看看它是否接近当前拖动的圆形
      // alldoms.forEach(dom => {
      //   const domCx = parseFloat(dom.getAttribute('cx'));
      //   const domCy = parseFloat(dom.getAttribute('cy'));
      //   const domR = parseFloat(dom.getAttribute('r'));

      //   const distance = Math.sqrt(Math.pow(newCx - domCx, 2) + Math.pow(newCy - domCy, 2));
      //   const threshold = parseFloat(circle.getAttribute('r')) + domR;

      //   // 只有在距离小于阈值时才触发反向移动
      //   if (distance < threshold) {
      //     // 如果两个圆形太接近，反向移动它们
      //     const moveX = (newCx - domCx) * 0.01;
      //     const moveY = (newCy - domCy) * 0.01;
      //     dom.setAttribute('cx', domCx - moveX);
      //     dom.setAttribute('cy', domCy - moveY);
      //     // dom与剩余球的检测
      //     this.pg(dom,this.def)
      //   }
      // });
    },

    // 鼠标松开时，停止拖动
    stop() {
      this.isActive = false;
      this.activeId = -1;
    },
    pg(box,def){
      const circle = box;
      let alldoms
      if(def){
         alldoms = Array.from(box.parentNode.querySelectorAll('circle:not(:is([id="' + def.id + '"],[id="' + box.id + '"])) '));
      }
      else{
         alldoms = Array.from(box.parentNode.querySelectorAll('circle:not([id="' + box.id + '"]) '));
      }
      
      // 检查每个圆形，看看它是否接近当前拖动的圆形
      alldoms.forEach(dom => {
        const newCx = parseFloat(box.getAttribute('cx'));
        const newCy = parseFloat(box.getAttribute('cy'));
        const domCx = parseFloat(dom.getAttribute('cx'));
        const domCy = parseFloat(dom.getAttribute('cy'));
        const domR = parseFloat(dom.getAttribute('r'));

        const distance = Math.sqrt(Math.pow(newCx - domCx, 2) + Math.pow(newCy - domCy, 2));
        const threshold = parseFloat(circle.getAttribute('r')) + domR;

        // 只有在距离小于阈值时才触发反向移动
        if (distance < threshold) {
          // 如果两个圆形太接近，反向移动它们
          const moveX = (newCx - domCx) * 0.01;
          const moveY = (newCy - domCy) * 0.01;
          dom.setAttribute('cx', domCx - moveX);
          dom.setAttribute('cy', domCy - moveY);
          // dom与剩余球的检测
          this.pg(dom, def);
    }
  });
  }
  }
};
</script>

<style></style>
