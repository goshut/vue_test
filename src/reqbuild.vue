<template>
    <div>
        <h2>Fetch Request Parser</h2>
        <textarea v-model="fetchStr" placeholder="Enter your fetch request here..."></textarea>
        <button @click="parseFetchStr">Parse</button>

        <div v-if="parsedData">
            <h3>Parsed Request</h3>
            <p><strong>Method:</strong> {{ parsedData.method }}</p>
            <p><strong>URL:</strong> {{ parsedData.url }}</p>
            <p><strong>Headers:</strong></p>
            <pre>{{ parsedData.headers }}</pre>
            <p><strong>Cookies:</strong></p>
            <pre>{{ parsedData.cookies }}</pre>
            <p><strong>Body Data:</strong></p>
            <pre>{{ parsedData.data }}</pre>
            <p><strong>JSON:</strong></p>
            <pre>{{ parsedData.json }}</pre>
        </div>
    </div>
</template>
  
<script>
import { ref } from 'vue';

export default {
    setup() {
        const fetchStr = ref('');
        const parsedData = ref(null);

        const parseFetchStr = () => {
            // Reset parsed data
            const data = {
                method: 'GET',
                url: '',
                headers: {},
                cookies: {},
                data: {},
                json: '',
            };

            // Split the input into lines and filter out irrelevant ones
            const lines = fetchStr.value.split('\n').filter(line => line.includes(':') || line.startsWith('fetch'));

            lines.forEach(line => {
                if (line.startsWith('fetch')) {
                    data.url = line.split('"')[1];
                } else if (line.includes(':')) {
                    const [key, value] = line.split(':').map(part => part.trim().replace(/["',]/g, ''));
                    if (key.toLowerCase() === 'method') {
                        data.method = value;
                    } else if (key.toLowerCase().startsWith('cookie')) {
                        value.split('; ').forEach(cookie => {
                            const [cookieName, cookieValue] = cookie.split('=');
                            data.cookies[cookieName] = cookieValue;
                        });
                    } else {
                        data.headers[key] = value;
                    }
                }
            });

            // Simulate body parsing
            // This part needs to be customized based on the structure of the body you expect

            parsedData.value = data;
        };

        return {
            fetchStr,
            parseFetchStr,
            parsedData,
        };
    },
};
</script>
  
<style>
textarea {
    width: 100%;
    min-height: 150px;
    margin-bottom: 10px;
}

pre {
    background-color: #f6f8fa;
    border-radius: 5px;
    padding: 10px;
}
</style>
  