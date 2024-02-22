<!--
https://eugenkiss.github.io/7guis/tasks/#circle
-->

<script setup>
import { onMounted, ref, provide } from 'vue'
import { getHighlighter } from 'shiki'
// import test0 from "./test0.vue"
// import reqbuild from "./reqbuild.vue"
import reqbuild2 from "./reqbuild2.vue"
const reqbuild2_export = ref(null)
// import { pythonFileStr, parsePythonCode } from "./reqbuild2.vue"

// let jj = {};
// const tt = ref("{}")
// tt.value = JSON.stringify(jj, null, 4)
// 测试字符串
// const fetchStr = `fetch("https://data.bilibili.com/v2/log/web?content_type=pbrequest&logid=021436&disable_compression=true", {
//   "headers": {
//     "accept": "*/*",
//     "accept-language": "zh-CN,zh;q=0.9,en;q=0.8",
//     "content-type": "text/plain",
//     "sec-ch-ua-mobile": "?0",
//     "sec-fetch-dest": "empty",
//     "sec-fetch-mode": "cors",
//     "sec-fetch-site": "same-site",
//     "cookie": "LIVE_BUVID=AUTO5915943877142556; buvid_fp_plain=undefined; CURRENT_BLACKGAP=0; buvid4=C8A61ED5-8A2B-F499-5BFD-0CFDFDB6A30136169-022012118-i%2BWtT6gDQ%2FAg9IEZK%2BpQ2A%3D%3D; is-2022-channel=1; hit-new-style-dyn=1",
//     "Referer": "https://www.bilibili.com/",
//     "Referrer-Policy": "no-referrer-when-downgrade"
//   },
//   "body": "from=en&to=zh&query=Co&transtype=translang&simple_means_flag=3&sign=2540.322269&token=79286ffdac97cd6a3d72b15e3ee38686&domain=common&ts=1708138824063",
//   "method": "POST",
//   "credentials": "omit"
// });`
// // 使用函数
// const result = parse_python_code(fetchStr);
// console.log(result);


// parsePythonCode(fetchStr)
// alert(pythonFileStr.value)
// console.log(pythonFileStr.value);

let highlighter = undefined;
// let python_code = `#!D:\\python\\virtualenv\\flask\\Scripts\\python.exe
// from flask import Flask, render_template, url_for

// @app.route("/")
// def root():
//     return "hii"

// if __name__ == "__main__":
//     app.run(port=9999, host="127.0.0.1", debug=False)`;
let fetch_str = ""
let old_fetch_str = ""
let code_html = ref(null)
const python_code_html = ref("")
// const python_code_html = computed(() => {
//   return highlighter.codeToHtml(pythonFileStr, {
//     lang: 'python',
//     theme: 'dracula'
//   })
// })
const error_msg = ref('')


function timed(fn, ...args) {
  // 开始测量
  console.time(`${fn.name}运行时间`);
  // 调用原始函数
  const result = fn(...args);
  // 结束测量
  console.timeEnd(`${fn.name}运行时间`);
  // 返回原始函数的结果
  return result;
}
function showNotification(message, duration = 500) {
  const notification = document.createElement('div');
  notification.textContent = message;
  notification.style.background = 'rgba(0, 0, 0, 0.7)';
  notification.style.color = '#fff';
  notification.style.padding = '10px 20px';
  notification.style.margin = '0 auto';
  notification.style.borderRadius = '5px';
  notification.style.maxWidth = '300px';
  notification.style.textAlign = 'center';
  notification.style.transition = 'all 0.5s';

  const container = document.getElementById('notification-container');
  container.appendChild(notification);

  // 在一段时间后自动移除通知
  setTimeout(() => {
    notification.style.opacity = '0';
    setTimeout(() => container.removeChild(notification), 500); // 确保淡出动画完成后再移除
  }, duration);
}
// 淡隐淡出效果
// let code_highlight_str_labe = undefined
const code_block_laber = ref(null)
function slow_show(_lab, _fun, _slow_time = 0.16, ...args) {
  Object.assign(_lab.style, {
    opacity: 0,
    transition: `opacity ${_slow_time}s`, // 使用模板字符串
  })
  setTimeout(() => {
    _fun(...args)
    Object.assign(_lab.style, {
      opacity: 1,
      transition: `opacity ${_slow_time}s`, // 使用模板字符串
    });
  }, _slow_time * 1000); // 延迟 1000 毫秒，也就是 1 秒
}
function slow_show_accurate(_lab, _fun_time_slow, _fun_time_fast, _slow_time = 0.16, args1 = [], args2 = []) {
  Object.assign(_lab.style, {
    opacity: 0,
    transition: `opacity ${_slow_time}s`, // 使用模板字符串
  })
  // 使用淡出时间粉饰耗时操作
  _fun_time_slow(...args1)
  setTimeout(() => {
    _fun_time_fast(...args2)
    Object.assign(_lab.style, {
      opacity: 1,
      transition: `opacity ${_slow_time}s`, // 使用模板字符串
    });
  }, _slow_time * 1000); // 延迟 1000 毫秒，也就是 1 秒
}
function get_highlight_html(p_python_code, res_value = Object) {
  // res_value.value = highlighter.codeToHtml(p_python_code, {
  //   lang: 'python',
  //   theme: 'dracula'
  // })
  res_value.value = timed(highlighter.codeToHtml, p_python_code, {
    lang: 'python',
    theme: 'dracula'
  })
  return res_value.value
}
function python_code_html_set_value(new_value) {
  python_code_html.value = new_value.value
}
function convertCodeToHtml(p_python_code) {
  python_code_html.value = highlighter.codeToHtml(p_python_code, {
    lang: 'python',
    theme: 'dracula'
  })
}
async function parse() {
  if (fetch_str.trim() === '') {
    error_msg.value = '请输入有效的字符串';
    showNotification(error_msg.value);
    return;
  }
  if (fetch_str === old_fetch_str) {
    error_msg.value = '你已经提交过这个字符串了';
    showNotification(error_msg.value);
    return;
  }
  error_msg.value = '';
  try {
    // const formData = new FormData();
    // formData.append('fetch_str', fetch_str.value);
    // const response = await fetch('http://127.0.0.1:5000/parse', {
    //   method: 'POST',
    //   // url: "http://127.0.0.1:5000",
    //   host: "127.0.0.1",
    //   headers: {
    //     'Content-Type': 'application/json',
    //   },
    //   body: JSON.stringify(fetch_str.value)
    //   // body: formData // js发送表单就是个垃圾!!!!
    // });
    // const textData = await response.text(); // 首先读取响应体为文本
    // try {
    //   // 尝试将文本解析为JSON
    //   const data = JSON.parse(textData);
    //   // 假设解析成功，处理你的数据
    //   python_code = data.python_code ? data.python_code : textData;
    // } catch (e) {
    //   // 如果解析JSON失败，假设是纯文本响应
    //   python_code = textData;
    // }
    old_fetch_str = fetch_str; // 更新 old_fetch_str
    // convertCodeToHtml(python_code)
    // if (reqbuild2_export.value.parsePythonCode(fetch_str)) {
    if (timed(reqbuild2_export.value.parsePythonCode, fetch_str)) {
      // slow_show(code_block_laber.value, convertCodeToHtml, 0.1, reqbuild2_export.value.pythonFileStr)
      slow_show_accurate(code_block_laber.value, get_highlight_html, python_code_html_set_value, 0.1, [reqbuild2_export.value.pythonFileStr, code_html], [code_html])
    }
    // convertCodeToHtml(pythonFileStr.value)//响应没那么强,次级依赖察觉不到的
    // console.log(pythonFileStr.value)
  } catch (error) {
    console.error('Error fetching data:', error);
    error_msg.value = '请求失败，请重试';
    showNotification(error_msg.value);
    // console.log(pythonFileStr.value)
  }
};
function copy() {
  if (navigator.clipboard) {
    navigator.clipboard.writeText(reqbuild2_export.value.pythonFileStr).then(() => {
      showNotification('代码已复制到剪贴板'); // 使用自定义通知
    }, () => {
      showNotification('复制失败'); // 使用自定义通知
    });
  } else {
    // 旧浏览器的备用方案
    const textarea = document.createElement('textarea');
    textarea.value = reqbuild2_export.value.pythonFileStr;
    document.body.appendChild(textarea);
    textarea.select();
    try {
      const successful = document.execCommand('copy');
      const msg = successful ? '复制成功' : '复制失败';
      console.log(msg);
    } catch (err) {
      showNotification('无法通过 execCommand 复制'); // 使用自定义通知
    }
    document.body.removeChild(textarea);
  }
};


// 使用 provide 函数，提供 showNotification 方法
provide("showNotification", showNotification)
onMounted(async () => {
  // code_highlight_str_labe = document.getElementById("code-highlight-str")
  // 你的异步逻辑
  highlighter = await getHighlighter({
    langs: ['python'],
    themes: ['dracula'],
  });
  // convertCodeToHtml(python_code)
  // convertCodeToHtml(reqbuild2_export.value.pythonFileStr)
  timed(convertCodeToHtml, reqbuild2_export.value.pythonFileStr)
});
</script>

<template>
  <!-- <test0 /> -->
  <!-- <reqbuild /> -->
  <!-- 传递 a1 给 reqbuild2.vue -->
  <reqbuild2 ref="reqbuild2_export"></reqbuild2>
  <!-- <div id="app"> -->
  <h1>Copilot Demo</h1>
  <!-- <p>在下面的框中输入你想解析的字符串，然后点击提交按钮，Copilot会帮你生成对应的python脚本。</p> -->
  <form id="fetch-form">
    <textarea id="fetch-str" name="fetch_str" rows="10" cols="80"
      placeholder="在下面的框中输入你想解析的字符串，然后点击提交按钮，Copilot会帮你生成对应的 Python 脚本。" v-model="fetch_str"></textarea>
  </form>
  <div id="buttons-container">
    <input id="submit-btn" type="button" value="提交" @click="parse">
    <!--<button id="copy-btn" @click="copy">复制代码</button>-->
  </div>
  <div id="code-block" ref="code_block_laber">
    <button id="copy-btn" @click="copy">复制代码</button>
    <code id="code-highlight-str" v-html="python_code_html"></code>
  </div>
  <div id="notification-container"></div>
  <!-- </div> -->
</template>

<style>
h1 {
  text-align: center;
  color: blue;
  margin-top: 10px;
  margin-bottom: 10px;
}

p {
  text-align: center;
}

#fetch-form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

#fetch-str {
  margin: 10px;
}

#submit-btn {
  margin: 10px;
  padding: 5px;
  background-color: green;
  transition: background-color 0.16s;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 5px;
}

#submit-btn:hover {
  background-color: lightgreen;
  /* transition: background-color 0.15s; */
}

#code-block {
  position: relative;
  /*display: inline-block;*/
  /*text-align: start;*/
  /*滑动条占40px*/
  max-width: 690px;
}

#copy-btn {
  position: absolute;
  top: 10px;
  right: 26px;
  z-index: 10;
  background-color: #6272A4;
  /* transition: background-color 5s; */
  /* 与 Shiki 主题颜色协调 */
  color: white;
  border: none;
  cursor: pointer;
  padding: 5px 10px;
  border-radius: 5px;
  opacity: 0;
  transition: opacity 0.16s, background-color 0.16s;
}

/* 当鼠标悬停在 #code-block 上时显示按钮 */
#code-block:hover #copy-btn {
  opacity: 0.8;
  /* transition: opacity 1s; */
}

/* 当鼠标悬停在 #copy-btn 上时显示按钮 */
#code-block:hover #copy-btn:hover {
  opacity: 1;
  /* 这里设置第二次变化的 opacity */
  background-color: #7588c2;
}

#code-highlight-str {
  /* color: #F8F8F2; */
  /* font-family: monospace; */
  /*margin: 0;*/
  /*padding: 0;*/
  /* overflow-y: auto; */
  display: flex;
}

#code-highlight-str pre {
  border-radius: 10px;
  margin: auto;
  padding: 16px;
  overflow: auto;
  max-width: 650px;
  /* 设置最大宽度 */
  max-height: 260px;
}

#notification-container {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
}

#buttons-container {
  display: flex;
  justify-content: center;
  gap: 10px;
}
</style>