<script setup>
import { onMounted } from 'vue';
// 储存测试:通过! 源:  file://
localStorage.setItem("wind_key", "scrollPosition");


console.log(window.location.pathname)
let iframe = undefined
function post_mess() {
    // const ff = window.frames[0]
    // ff.postMessage('test', "*");
    iframe.contentWindow.postMessage('test', "*");
}
onMounted(async () => {
    iframe = document.getElementById('yourIframeId')
    // 向 <iframe> 发送消息请求滚动位置
    // document.getElementById('yourIframeId').contentWindow.postMessage('getScrollPosition', '*');
    // iframe.contentWindow.postMessage('test', '*');

    // 监听来自 <iframe> 的响应
    window.addEventListener('message', event => {
        alert("收到了: " + event.origin + "-->>" + event.data)
        console.log("main:")
        console.log(event.data)
        console.log(event.origin)
        console.log(event.source)
        // 确保事件来源是你的 <iframe>
        if (event.origin === 'http://你的iframe域名') {
            console.log('Received scroll position from iframe:', event.data);
        }

    });
})

</script>
<template>
    <div>
        <h1>main_</h1><button @click="post_mess">ttt</button>
    </div>
</template>