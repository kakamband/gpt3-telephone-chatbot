# Build a Telephone Chatbot with GPT-3 and Twilio Autopilot

![Drag Racing](https://twilio-cms-prod.s3.amazonaws.com/images/ffDYsc4pz8pN00cRQ95CN4hhOeXjn9y8_vuLJlKaEdSAJ.width-1616.png)

This is the source code for the tutorial, [Build a Telephone Chatbot with GPT-3 and Twilio Autopilot](https://www.twilio.com/blog/build-telephone-chatbot-gpt3-twilio-autopilot). The final function code is in the file named [function-code-2.js](function-code-2.js);


## Function Code - Step 1
```javascript
exports.handler = async (context, event, callback) => {
  const response = {};

  response.actions = await fallbackHandler(event);

  callback(null, response);
};

const fallbackHandler = (event) => {
  const actions = [];

  actions.push({ say: `I heard you say: ${event.CurrentInput}` });
  actions.push({ listen: true });

  return actions;
};
```

## Function Code - Step 2
```javascript
const axios = require('axios');

exports.handler = async (context, event, callback) => {
  const response = {};

  response.actions = await fallbackHandler(event);

  callback(null, response);
};

const fallbackHandler = async (event) => {
  const actions = [];

  const instance = axios.create({
    baseURL: 'https://api.openai.com/v1/',
    headers: { Authorization: `Bearer ${process.env.OPENAI_API_KEY}` },
  });

  const dialog = [
    'bot: Hello, how are you today?',
  ];

  dialog.push(`human: ${event.CurrentInput}`);
  dialog.push('bot:');

  const completionParmas = {
    prompt: dialog.join('\n'),
    max_tokens: 75,
    temperature: 0.65,
    n: 1,
    stream: false,
    logprobs: null,
    echo: false,
    stop: '\n',
  };

  try {
    const result = await instance.post('/engines/davinci/completions', completionParmas);
    const botResponse = result.data.choices[0].text.trim();
    actions.push({ say: botResponse });
  } catch (err) {
    console.log(err);
    actions.push({ say: 'Sorry. Something went wrong. Can you say that again?' });
  }

  actions.push({ listen: true });
  return actions;
};

```
