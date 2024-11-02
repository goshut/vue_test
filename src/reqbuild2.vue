<script setup>
import { ref, computed, reactive, inject } from 'vue'
const showNotification = inject('showNotification');

const method = ref("GET");
const url = ref("http://example.com");
const params = ref("{}");
const headers = ref("{}");
const cookies = ref("{}");
const data = ref("{}");
const json = ref("{}");
// const python_file_str = ref("")
const pythonFileStr = computed(() => {
    // 构建 variable_data 映射
    const variableData = reactive({ params: params.value, headers: headers.value, cookies: cookies.value, data: data.value, json: json.value });

    // 构建变量定义部分
    let variableDefinitions = '';
    for (const [key, value] of Object.entries(variableData)) {
        if (value && value !== '{}') { // 假定空对象不输出
            variableDefinitions += `${key} = ${value}\n`;
        }
    }

    // 构建请求参数部分
    let requestParams = '';
    for (const [key, value] of Object.entries(variableData)) {
        if (value && value !== '{}') {
            requestParams += `, ${key}=${key}`;
        }
    }

    // 最终的 Python 文件字符串
    return `import requests
from pprint import pprint

method = "${method.value}"
url = "${url.value}"
${variableDefinitions}response = requests.request(method, url${requestParams})
try:
    pprint(response.json())
except requests.exceptions.JSONDecodeError:
    print(response.text)
except Exception as e:
    print(e)`;
});
// function preprocessStr(str) {
//     // 替换所有的 \" 为 \\"，以确保 JSON 解析时转义字符被正确处理
//     // return str.replace(/\\+"/g, '\\\\"');
//     return str;
// }
// export function parse_python_code(fetchStr) {
//     // 提取 URL
//     const urlMatch = fetchStr.match(/fetch\("([^"]+)"\s*,/);
//     const url = urlMatch ? urlMatch[1] : '';

//     // 将 URL 分解为基础 URL 和参数
//     const [baseUrl, queryString] = url.split('?');
//     const params = Object.fromEntries(new URLSearchParams(queryString));

//     // 尝试将剩余字符串转换为 JSON
//     const jsonStrStart = fetchStr.indexOf('{', fetchStr.indexOf('fetch'));
//     const jsonStrEnd = fetchStr.lastIndexOf('}');
//     const jsonStr = fetchStr.substring(jsonStrStart, jsonStrEnd + 1);

//     try {
//         const fetchOptions = JSON.parse(jsonStr);
//         let { headers, body, method, ...otherOptions } = fetchOptions;

//         // 提取并转换 Cookies
//         const cookies = headers.cookie ? Object.fromEntries(headers.cookie.split('; ').map(cookie => cookie.split('='))) : {};
//         delete headers.cookie; // 从 headers 中移除 cookie

//         // 处理 Body
//         let data = {};
//         let json = {};
//         if (body && body.startsWith('{')) {
//             json = JSON.parse(body);
//         } else {
//             data = body; // 直接使用字符串，或者进一步解析为键值对
//         }

//         // 将其他选项加入到 headers 中
//         headers = { ...headers, ...otherOptions };

//         return {
//             baseUrl,
//             params,
//             method,
//             headers,
//             cookies,
//             data,
//             json,
//         };
//     } catch (e) {
//         console.error('Error parsing fetch string', e);
//         return {};
//     }
// };


// export function parsePythonCode(fetchStr) {
//     // 提取 URL
//     const urlMatch = fetchStr.match(/fetch\("([^"]+)"\s*,/);
//     const raw_url = urlMatch ? urlMatch[1] : '';

//     // 将 URL 分解为基础 URL 和参数
//     let queryString = '';
//     [url.value, queryString] = raw_url.split('?');
//     params.value = Object.fromEntries(new URLSearchParams(queryString));

//     // 尝试将剩余字符串转换为 JSON
//     const jsonStrStart = fetchStr.indexOf('{', fetchStr.indexOf('fetch'));
//     const jsonStrEnd = fetchStr.lastIndexOf('}');
//     const jsonStr = fetchStr.substring(jsonStrStart, jsonStrEnd + 1);

//     try {
//         const fetchOptions = JSON.parse(jsonStr);
//         let { headers, body, method, ...otherOptions } = fetchOptions;

//         // 提取并转换 Cookies
//         cookies.value = headers.value.cookie ? Object.fromEntries(headers.value.cookie.split('; ').map(cookie => cookie.split('='))) : {};
//         delete headers.value.cookie; // 从 headers 中移除 cookie

//         // 处理 Body
//         // let data = {};
//         // let json = {};
//         if (body && body.startsWith('{')) {
//             json.value = JSON.parse(body);
//         } else {
//             data.value = body; // 直接使用字符串，或者进一步解析为键值对
//         }

//         // 将其他选项加入到 headers 中
//         headers.value = { ...headers, ...otherOptions };

//         // return {
//         //     baseUrl,
//         //     params,
//         //     method,
//         //     headers,
//         //     cookies,
//         //     data,
//         //     json,
//         // };
//     } catch (e) {
//         console.error('Error parsing fetch string', e);
//         return {};
//     }
// }
// export function parsePythonCode(fetchStr) {
//     // 提取 URL
//     const urlMatch = fetchStr.match(/fetch\("([^"]+)"\s*,/);
//     const raw_url = urlMatch ? urlMatch[1] : '';

//     // 将 URL 分解为基础 URL 和参数
//     let queryString = '';
//     [url.value, queryString] = raw_url.split('?');
//     params.value = Object.fromEntries(new URLSearchParams(queryString));

//     // 尝试将剩余字符串转换为 JSON
//     const jsonStrStart = fetchStr.indexOf('{', fetchStr.indexOf('fetch'));
//     const jsonStrEnd = fetchStr.lastIndexOf('}');
//     const jsonStr = fetchStr.substring(jsonStrStart, jsonStrEnd + 1);

//     try {
//         const fetchOptions = JSON.parse(jsonStr);
//         let { __headers, __body, method: methodOption, ...otherOptions } = fetchOptions;

//         // 更新方法
//         method.value = methodOption || 'GET'; // 确保有默认值

//         // 更新 URL 和 Params
//         // [url.value, queryString] = raw_url.includes('?') ? raw_url.split('?') : [raw_url, ''];
//         params.value = queryString ? JSON.stringify(Object.fromEntries(new URLSearchParams(queryString))) : '{}';

//         // 更新 Headers，排除 Cookies
//         if (__headers && __headers.cookie) {
//             cookies.value = JSON.stringify(Object.fromEntries(__headers.cookie.split('; ').map(_ => _.split('='))));
//             delete __headers.cookie; // 从 headers 中移除 cookie
//         } else {
//             cookies.value = '{}';
//         }
//         headers.value = JSON.stringify({ ...headers, ...otherOptions });

//         // 更新 Data 或 Json
//         if (__body) {
//             if (__body.startsWith('{')) {
//                 json.value = __body; // 保留 JSON 字符串
//                 data.value = '{}';
//             } else {
//                 data.value = __body;
//                 json.value = '{}';
//             }
//         }
//     } catch (e) {
//         console.error('Error parsing fetch string', e);
//         return {};
//     }
// }
// export default {
//     setup() {
//         const showNotification = inject('showNotification')
//         return { showNotification }
//     }
// }
// showNotification("test0......")
// const props = defineProps({
//     showNotification: Function,
// });

function parsePythonCode(fetchStr) {
    // 使用正则表达式从 fetchStr 中提取 URL
    const __urlMatch = fetchStr.match(/fetch\("([^"]+)"\s*,/);
    const __url = __urlMatch ? __urlMatch[1] : ''; // 如果匹配成功，获取 URL，否则为空字符串

    // 将 URL 分解为基础 URL 和查询字符串
    const [baseUrl, queryString] = __url.split('?');
    // 将查询字符串转换为对象
    const __params = Object.fromEntries(new URLSearchParams(queryString));

    // 尝试从 fetchStr 中提取 JSON 字符串
    const __jsonStrStart = fetchStr.indexOf('{', fetchStr.indexOf('", '));
    const __jsonStrEnd = fetchStr.lastIndexOf('}');
    const __jsonStr = fetchStr.substring(__jsonStrStart, __jsonStrEnd + 1); // 提取 JSON 字符串

    try {
        // 解析 JSON 字符串为对象
        const fetchOptions = JSON.parse(__jsonStr);
        // 解构 fetchOptions，提取 headers、body 和 method
        let { headers: __headers, body: __body, method: __method, ...otherOptions } = fetchOptions;

        // 提取并转换 Cookies
        const __cookies = {};
        if (__headers.cookie) {
            // 将 cookie 字符串按 '; ' 分割，并处理每个 cookie
            __headers.cookie.split('; ').forEach(cookie => {
                const [key, ...valueParts] = cookie.split('='); // 分割为键和值
                const value = valueParts.join('='); // 处理值中可能存在的 '='
                __cookies[key] = value; // 将键值对存入 __cookies 对象
            });
        }
        delete __headers.cookie; // 从 __headers 中移除 cookie

        // 处理请求体
        let __data = {};
        let __json = {};
        if (__body && __body.startsWith('{')) {
            // 如果 body 是 JSON 格式，解析为对象
            __json = JSON.parse(__body);
        } else if (__body != null) {
            // 否则，将 body 解析为键值对对象
            __data = Object.fromEntries(new URLSearchParams(__body));
        }

        // 将其他选项合并到 __headers 中
        __headers = { ...__headers, ...otherOptions };
        // 添加 User-Agent 头
        __headers["User-Agent"] = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36";

        // 将解析后的值赋给 Vue.js 的响应式变量
        url.value = baseUrl; // 基础 URL
        params.value = JSON.stringify(__params, null, 4); // 查询参数
        method.value = __method; // 请求方法
        headers.value = JSON.stringify(__headers, null, 4); // 请求头
        cookies.value = JSON.stringify(__cookies, null, 4); // Cookies
        data.value = JSON.stringify(__data, null, 4); // 请求体数据
        json.value = JSON.stringify(__json, null, 4); // JSON 数据

        return true; // 解析成功，返回 true
    } catch (e) {
        // 捕获解析错误，显示通知并打印错误信息
        showNotification("无效字符串");
        console.log(e);
        return false; // 解析失败，返回 false
    }
}




function parsePythonCode_old(fetchStr) {
    //导入测试
    // showNotification("test......")
    // 提取 URL
    const __urlMatch = fetchStr.match(/fetch\("([^"]+)"\s*,/);
    const __url = __urlMatch ? __urlMatch[1] : '';

    // 将 URL 分解为基础 URL 和参数
    const [baseUrl, queryString] = __url.split('?');
    const __params = Object.fromEntries(new URLSearchParams(queryString));

    // 尝试将剩余字符串转换为 JSON
    const __jsonStrStart = fetchStr.indexOf('{', fetchStr.indexOf('", '));
    const __jsonStrEnd = fetchStr.lastIndexOf('}');
    const __jsonStr = fetchStr.substring(__jsonStrStart, __jsonStrEnd + 1);

    try {
        const fetchOptions = JSON.parse(__jsonStr);
        let { headers: __headers, body: __body, method: __method, ...otherOptions } = fetchOptions;

        // 提取并转换 Cookies
        const __cookies = __headers.cookie ? Object.fromEntries(__headers.cookie.split('; ').map(cookie => cookie.split('='))) : {};
        delete __headers.cookie; // 从  __headers 中移除 cookie

        // 处理 Body
        let __data = {};
        let __json = {};
        if (__body && __body.startsWith('{')) {
            __json = JSON.parse(__body);
        } else if (__body != null) {
            __data = Object.fromEntries(new URLSearchParams(__body)); // 直接使用字符串，或者进一步解析为键值对
        }

        // 将其他选项加入到  __headers 中
        __headers = { ...__headers, ...otherOptions };
        // 添加use-gen
        __headers["User-Agent"] = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36"

        url.value = baseUrl
        params.value = JSON.stringify(__params, null, 4); // 2表示每个缩进的空格数
        method.value = __method
        headers.value = JSON.stringify(__headers, null, 4);
        cookies.value = JSON.stringify(__cookies, null, 4);
        data.value = JSON.stringify(__data, null, 4);
        json.value = JSON.stringify(__json, null, 4);

        // //一次性更新 ,天然一次性更新
        // const state = reactive({
        //     url: '',
        //     params: '',
        //     method: '',
        //     headers: '',
        //     cookies: '',
        //     data: '',
        //     json: '',
        // });

        // const updateAllAtOnce = () => {
        //     state.url = baseUrl;
        //     state.params = JSON.stringify(__params, null, 4);
        //     state.method = __method;
        //     state.headers = JSON.stringify(__headers, null, 4);
        //     state.cookies = JSON.stringify(__cookies, null, 4);
        //     state.data = JSON.stringify(__data, null, 4);
        //     state.json = JSON.stringify(__json, null, 4);
        // }
        // console.log(cookies.value)
        // updateAllAtOnce()
        return true;
    } catch (e) {
        // console.error('Error parsing fetch string', e);
        showNotification("无效字符串")
        console.log(e)
        return false;
    }
};
// 使用 defineExpose 函数，提供一个对象，包含 pythonFileStr 和 parsePythonCode
defineExpose({
    pythonFileStr,
    parsePythonCode,
})
</script>

<template>

</template>