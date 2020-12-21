<template>
  <div id="app">
    <div class='slot-machine'>
      <button @click='start'>start</button>
      <div
        class='slot'
        v-for='slot in slots'
        :key="slot.title"
        ref='slots'
      >
        <div class='slot__window'>
          <div class='slot__wrap'>
            <div
              class='slot__item'
              v-for='(opt, i) in slot.items'
              :key="i"
            >{{ opt }}</div>
            <div class='slot__item slot__item--copy'>{{ slot.items[0] }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const next =
  window.requestAnimationFrame ||
  window.webkitRequestAnimationFrame ||
  window.mozRequestAnimationFrame ||
  window.msRequestAnimationFrame ||
  window.oRequestAnimationFrame ||
  function (cb) {
    window.setTimeout(cb, 1000 / 60);
  };

export default {
  name: "App",
  data() {
    return {
      slots: [
        {
          title: "1",
          items: [
            "0",
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
          ],
        },
        {
          title: "2",
          items: [
            "0",
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
          ],
        },
        {
          title: "3",
          items: [
            "0",
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
          ],
        },
        {
          title: "4",
          items: [
            "0",
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
          ],
        },
        {
          title: "5",
          items: [
            "0",
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
          ],
        },
        {
          title: "6",
          items: [
            "0",
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
          ],
        },
      ],
      opts: null,
      startedAt: null,
      // 最终中奖号码
      code: null,
      codes: ["983023", "608421", "948988", "037111", "553243"],
      done: false,
    };
  },
  mounted() {
    window.addEventListener("keyup", (e) => {
      if (e.keyCode === 32 && !this.opts) {
        this.code = this.codes.shift().split("");
        this.start();
        // this.done = true
      }
    });
  },
  methods: {
    start: function () {
      if (this.opts) {
        return;
      }
      this.opts = this.slots.map((data, i) => {
        const height = document.querySelector('.slot__item').offsetHeight;
        const slot = this.$refs.slots[i];
        // map(function(){})利用map便利slots的每一个选项组,最终得到return的返回值
        // const choice = Math.floor(Math.random() * data.items.length); // 随机生成一个[0,data.items.length]的整数(floor向下取整)

        const choice = data.items.findIndex((v) => this.code[i] === `${v}`);

        const opts = {
          el: slot.querySelector(".slot__wrap"), //指向奖项元素的父级;
          finalPos: choice * height, // 180为每一个奖品滚动标签的高度;
          // startOffset: 2000 + Math.random() * 500 + i * 500,// 影响转的圈数
          startOffset: 2000 + Math.random() * 100 + (i + 2) * 500, // 影响转的圈数
          height: data.items.length * height,
          duration: 3000 + i * 1000, // milliseconds
          isFinished: false,
        };
        return opts;
      });
      //    console.log(this.opts);//这个时候this.opts已经和opts一模一样了
      next(this.animate); // 开启动画
    },
    animate: function (timestamp) {
      // timestamp当前的方法持续的毫秒数
      if (this.startedAt == null) {
        this.startedAt = timestamp; // 动画初始时间
      }
      const timeDiff = timestamp - this.startedAt; //动画持续的时间
      this.opts.forEach((opt) => {
        if (opt.isFinished) {
          return;
        }
        const timeRemaining = Math.max(opt.duration - timeDiff, 0); // 总的持续时间 - 动画持续时间 = 剩下的时间,0表示结束
        const power = 3;
        const offset =
          (Math.pow(timeRemaining, power) / Math.pow(opt.duration, power)) *
          opt.startOffset;
        // Math.pow(timeRemaining, power)表示: timeRemaining 的3 次幂;
        // negative, such that slots move from top to bottom
        const pos = -1 * Math.floor((offset + opt.finalPos) % opt.height);
        opt.el.style.transform = "translateY(" + pos + "px)";

        if (timeDiff > opt.duration) {
          //        console.log('finished', opt, pos, opt.finalPost)
          opt.isFinished = true;
        }
      });
      if (this.opts.every((o) => o.isFinished)) {
        this.opts = null;
        this.startedAt = null;
        //      console.log('finished')
      } else {
        next(this.animate);
      }
    },
  },
};
</script>

<style>
#app {
  text-align: center;
}
* {
  padding: 0;
  margin: 0;
}

body {
  height: 100vh;
  background: url("./assets/hd_bg.png") center center no-repeat;
  background-size: 100% 100%;
}

.slot-machine {
  padding: 410px 0 0;
  margin: 0 auto;
  display: flex;
  justify-content: center;
}

button {
  display: none;
}

.slot {
  float: left;
  margin: 0.4em;
}

.slot__window {
  background: url("./assets/num_bg.png") center center no-repeat;
  background-size: 100% 100%;
  width: 139px;
  height: 166px;
  overflow: hidden;
}

.slot__wrap {
}

.slot__item {
  width: 100%;
  text-align: center;
  color: #4b4b4b;
  font-size: 78px;
  font-weight: bold;
  line-height: 166px;
}

.slot__item--copy {
}
@media screen and (min-width: 1640px) {
    .slot-machine {
      padding-top: 499px;
    }
    .slot {
      /* margin: px; */
    }
    .slot__window {
      width: 166px;
      height: 199px;
    }
    .slot__item {
      line-height: 199px;
    }
}
</style>
