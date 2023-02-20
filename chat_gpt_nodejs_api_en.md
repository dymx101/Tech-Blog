### Initiate project
```
npm init -y
```
### Install dependencies
```
npm install express
npm install openai
npm install dotenv
```
### Env variables
1. Create `.env` file in project's root folder.
2. Mofify `.env` file, add API key.
```
OPENAI_API_KEY="你的chatGPT API Key"
```
### Write the main script: index.js
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
### Run test server on local machine
node index.js

### Send request to local server
`Local Api address`:
http://localhost:3000/api/completions

`Post请求body`:
```
{
  "prompt": "why did Russia start war with Ukraine?"
}
```
### Response returned example
```
{
    "completion": "\n\nIt is difficult to answer this question definitively as the reasons behind the Russian"
}
```
NOTE: The returned answer from api is truncated, not sure if this is due to I am using free version of ChatGPT. If you have paid $20 per month, please let me know if there's still truncation.

### Deploy service
You can deploy it to your server, here's a free nodeJS server you can use https://www.cyclic.sh/pricing.

### References
https://www.youtube.com/watch?v=c7MnlFhJZMI

`ChatGPT Prompt`: write a nodejs restful api which accept a string prompt parameter, then call the openAI endpoint to get a completion, and response with the completion to the caller
