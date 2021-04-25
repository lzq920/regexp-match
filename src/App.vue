<template>
  <div class="container">
    <div class="reg">
      <span>/</span>
      <input type="text" v-model.trim="reg" class="reg-input" maxlength="50" @input="handleChange">
      <span>/</span>
      <input type="text" v-model.trim="flags" class="flag-input" maxlength="5" @input="handleChange">
    </div>
    <div class="content">
      <input type="text" v-model.trim="text" class="test-input" autofocus @input="handleChange" maxlength="50">
      <span class="match-tag" v-for="(item,index) in matchs" :key="index" :style="item"></span>
    </div>
    <div class="tip">
      <div class="tip-item" v-for="(item,index) in tipList" :key="index" @click="appendReg(item.reg)">
        {{ item.reg }}
        <div class="tip-item-hover">{{ item.tip }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import {ref, onMounted} from 'vue'

const reg = ref('eleven')
const flags = ref('g')
const matchs = ref([])
const text = ref('eleven2333')
const tipList = ref([{
  reg: '.',
  tip: '匹配除换行符以外的任意字符'
}, {
  reg: '\\w',
  tip: '匹配字母或数字或下划线'
}, {
  reg: '\\s',
  tip: '匹配任意的空白符'
}, {
  reg: '\\d',
  tip: '匹配数字'
}, {
  reg: '\\b',
  tip: '匹配单词的开始或结束'
}, {
  reg: '^',
  tip: '匹配字符串的开始'
}, {
  reg: '$',
  tip: '匹配字符串的结束'
}, {
  reg: '*',
  tip: '重复零次或更多次'
}, {
  reg: '+',
  tip: '重复一次或更多次'
}, {
  reg: '?',
  tip: '重复零次或一次'
}, {
  reg: '{n}',
  tip: '重复n次'
}, {
  reg: '{n,}',
  tip: '重复n次或更多次'
}, {
  reg: '{n,m}',
  tip: '重复n到m次'
}, {
  reg: '\\W',
  tip: '匹配任意不是字母，数字，下划线，汉字的字符'
}, {
  reg: '\\S',
  tip: '匹配任意不是空白符的字符'
}, {
  reg: '\\D',
  tip: '匹配任意非数字的字符'
}, {
  reg: '\\B',
  tip: '匹配不是单词开头或结束的位置'
}, {
  reg: '[^x]',
  tip: '匹配除了x以外的任意字符'
}, {
  reg: '[^abc]',
  tip: '匹配除了abc这几个字母以外的任意字符'
}, {
  reg: '[abc]',
  tip: '匹配abc这几个字符中任意字符'
}, {
  reg: '[a-z]',
  tip: '匹配指定范围内的任意字符'
}, {
  reg: '[!a-z]',
  tip: '匹配任何不在指定范围内的任意字符'
}]);
const throttle = (fn, wait) => {
  let inThrottle, lastFn, lastTime;
  return function () {
    const context = this,
        args = arguments;
    if (!inThrottle) {
      fn.apply(context, args);
      lastTime = Date.now();
      inThrottle = true;
    } else {
      clearTimeout(lastFn);
      lastFn = setTimeout(function () {
        if (Date.now() - lastTime >= wait) {
          fn.apply(context, args);
          lastTime = Date.now();
        }
      }, Math.max(wait - (Date.now() - lastTime), 0));
    }
  };
};
const isRegexp = (value) => {
  return Object.prototype.toString.call(value) === '[object RegExp]';
}
const randomHexColorCode = () => {
  let n = (Math.random() * 0xfffff * 1000000).toString(16);
  return '#' + n.slice(0, 6);
};
const matchRegexp = () => {
  matchs.value = [];
  if (!reg.value.length) {
    return
  }
  if (!flags.value.length) {
    return
  }
  if (!text.value.length) {
    return
  }
  try {
    let regexp = new RegExp(reg.value, flags.value);
    if (!isRegexp(regexp)) {
      return
    } else {
      console.log(regexp)
    }
    let temp;
    let result = [];
    let count = 0;
    while ((temp = regexp.exec(text.value)) !== null && count < 100) {
      result.push(temp);
      count++;
    }
    for (let item of result) {
      let len = 0;
      item.forEach(i => {
        len += i.length;
      })
      matchs.value.push({
        left: item.index + 'ch',
        width: len + 'ch',
        borderBottom: `4px solid ${randomHexColorCode()}`
      });
    }
  } catch (e) {
    console.log(e)
  }
}
const handleChange = throttle(() => {
  matchRegexp()
}, 1000)
const appendReg = (str) => {
  reg.value += str;
}
onMounted(() => {
  matchRegexp()
})
</script>

<style>
html, body {
  position: relative;
  width: 100vw;
  height: 100vh;
  padding: 0;
  margin: 0;
  font-size: 36px;
  background-image: linear-gradient(135deg, #FDD819 0%, #E80505 100%)
}

* {
  box-sizing: border-box;
  font-family: Consolas, Monaco, monospace;
}

.container {
  position: relative;
  width: 1200px;
  height: 100vh;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.reg {
  position: relative;
  display: flex;
  justify-content: space-around;
  align-items: center;
  height: 40px;

}

.reg span {
  height: 40px;
  line-height: 40px;
  font-size: 36px;
  color: #ffffff;
}

.reg input {
  height: 40px;
  font-size: 36px;
  background-color: transparent;
  color: #ffffff;
  border: 1px dashed;
}

.reg input:focus {
  outline: none;
}

.reg .reg-input {
  flex: 1;
  margin: 0 auto;
}

.reg .flag-input {
  width: 80px;
}

.content {
  position: relative;
  width: 100%;
  margin-top: 50px;
}

.content .test-input {
  height: 40px;
  font-size: 36px;
  z-index: 1;
  background-color: transparent;
  color: #ffffff;
  width: 100%;
  border: 1px dashed;
}

.content .test-input:focus {
  outline: none;
}

.content .match-tag {
  position: absolute;
  height: 100%;
  top: 0;
  left: 0;
  transition: all 300ms;
}

.tip {
  margin-top: 100px;
  display: flex;
  flex-wrap: wrap;
  gap: 0.2rem;
}

.tip .tip-item {
  position: relative;
  padding: 4px 10px;
  font-size: 20px;
  border: 1px solid #f5f5f5;
  cursor: pointer;
  color: #ffffff;
  border-radius: 4px;
}

.tip .tip-item:hover {
  background-color: #9C27B0;
}

.tip .tip-item .tip-item-hover {
  position: absolute;
  display: none;
  left: 0;
  top: -200%;
  white-space: nowrap;
  background: #9C27B0;
  color: white;
  padding: 16px;
  font-size: 16px;
  transition: all 300ms;
}

.tip .tip-item:hover > .tip-item-hover {
  display: block;
}
</style>
