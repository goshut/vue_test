<script setup>
import { onMounted, ref } from 'vue'
import { getHighlighter } from 'shiki'
// const highlighter = null;
// reactive state
const count = ref(0)
const rawHtml = ref()
const python_code_html = ref()
// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
alert("ppp")
rawHtml.value = `<span style="color: blue">This should be bbbbbbbb.</span>`
alert("ppp")
rawHtml.value = `<span style="color: blue">This should be qwerewrew.</span>`
python_code_html.value = `<span style="color: green">This should be qwerewrew.</span>`
const python_code = `#!D:\\python\\virtualenv\\flask\\Scripts\\python.exe
from flask import Flask, render_template, url_for

@app.route("/")
def root():
    return "hii"

if __name__ == "__main__":
    app.run(port=9999, host="127.0.0.1", debug=False)`;
// async function convertCodeToHtml() {//需要在异步函数中使用!!!!!!!!!!!!
//   python_code_html.value = await codeToHtml(python_code, {
//     lang: 'python',
//     theme: 'dracula'
//   })
// }
// convertCodeToHtml()
onMounted(async () => {
  // 你的异步逻辑
  const highlighter = await getHighlighter({
    langs: ['python'],
    themes: ['dracula'],
  });
  python_code_html.value = highlighter.codeToHtml(python_code, {
    lang: 'python',
    theme: 'dracula'
  })
});
</script>

<template>
  <button @click="increment">
    Count is: {{ count }}
  </button>
  <p>Using text interpolation: {{ rawHtml }}</p>
  <p>Using v-html directive: <span id="789" v-html="rawHtml"></span></p>
  <pre id="code-block" v-html="python_code_html"></pre>
  <Suspense>
    <template #default>
      <pre id="code-block" v-html="python_code_html"></pre>
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>