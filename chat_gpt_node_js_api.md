### 初始化工程
```
npm init -y
```
### 安装依赖
```
npm install express
npm install openai
npm install dotenv --save
```
### 环境变量
1. 在工程根目录创建 `.env`文件
2. 编写`.env`文件
```
OPENAI_API_KEY="你的chatGPT API Key"
```
### 编写 index.js
```
const { Configuration, OpenAIApi } = require("openai");
require("dotenv").config();
const express = require('express');
const app = express();

const configuration = new Configuration({
    apiKey: process.env.OPENAI_API_KEY,
});
const openai = new OpenAIApi(configuration);

async function runCompletion(prompt) {
    const completion = await openai.createCompletion({
        model: "text-davinci-003",
        prompt: prompt,
    })

    return completion.data.choices[0].text;
}


app.use(express.json());

app.post('/api/completions', async (req, res) => {
    const { prompt } = req.body;
    try {
        const text = await runCompletion(prompt);
        console.log(text);
        res.json({ completion: text });
    } catch (error) {
        console.error(error);
        res.status(500).json({ error: 'Failed to get completion' });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```
### 测试运行服务器
node index.js

### 请求本地测试服务器
http://localhost:3000/api/completions

`Post请求body`:
```
{
  "prompt": "why did Russia start war with Ukran?"
}
```
### 返回response
```
{
    "completion": "\n\nIt is difficult to answer this question definitively as the reasons behind the Russian"
}
```
NOTE: 问题，返回结果被截断，可能是因为使用chatGPT免费版本的问题？

### 部署服务器
可以使用 https://www.cyclic.sh/pricing 免费版部署

### 参考资料
https://www.youtube.com/watch?v=c7MnlFhJZMI

`ChatGPT Prompt`: write a nodejs restful api which accept a string prompt parameter, then call the openAI endpoint to get a completion, and response with the completion to the caller
