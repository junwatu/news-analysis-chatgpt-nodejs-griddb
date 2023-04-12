# How to Leverage ChatGPT, Node.js, and GridDB for Automated News Tagging

In today's fast-paced digital age, we are constantly bombarded with an immense volume of news articles across numerous platforms, ranging from social media to online news websites. This massive influx of information, often referred to as the "news flood," presents several challenges for both consumers and news organizations. Addressing these challenges, which include content organization, information overload, time-consuming manual tagging processes, and inefficient content discovery and recommendations, is essential for improving the user experience and the overall quality of news consumption.

One solution to the problems is to use news tagging. It plays a crucial role in processing, organizing, and presenting the wealth of information available in the digital news domain. Manually tagging countless news articles with accurate topics is an overwhelming and time-consuming task.

_What if there were an automated content tagging system that could efficiently process and categorize news articles by topic in real-time?_

In this blog post, we'll create an **automated content tagging system**. We'll delve into how the synergy of OpenAI, Node.js, and GridDB can create a powerful topic modeling solution to revolutionize automated content tagging in the digital news landscape. We'll leverage Node.js to process news data, ChatGPT to analyze and extract topics from the data, and GridDB to store and retrieve the information. Ultimately, we'll present the organized data to users.

## Auto Content Tagging System Overview

ChatGPT, developed by OpenAI, is a cutting-edge language model that excels in understanding and generating human-like text. Node.js is a popular open-source runtime environment built on Chrome's V8 JavaScript engine, enabling developers to create scalable and efficient web applications. GridDB is a highly performant NoSQL database designed for handling time-series data and large-scale datasets, with features such as automatic data partitioning and high availability.

--- REMOVE ---

### Node.js

Node.js serves as the backbone of the web application, allowing developers to build a fast, scalable, and reliable system for processing and managing news data. With its non-blocking, event-driven architecture and extensive package ecosystem, Node.js makes it easy to implement complex features, such as real-time data processing and API integration.

### GridDB

To store and manage the vast amounts of news data and extracted topics, GridDB comes into play as the data storage solution. Its high-performance capabilities, support for horizontal scaling, and time-series data handling make it an ideal choice for managing the large-scale datasets involved in news topic modelling. Moreover, GridDB's efficient querying and data retrieval features ensure that the application can quickly serve relevant content to users.

By combining the strengths of ChatGPT, Node.js, and GridDB, the powerful news topic modelling system can effectively analyze, organize, and present news content, providing users with a more meaningful and engaging experience.

--- END REMOVE ---

![system-arch](assets/images/system-arch.svg)

In the given flow diagram, the interaction between ChatGPT, Node.js, and GridDB is illustrated, showcasing the overall system workflow for multi-news data acquisition, processing, and content tagging. The diagram can be broken down into the following steps for a better understanding:

1. Data Acquisition: Node.js is employed to collect and acquire data from multiple news sources.

2. Data Processing: Once the data is obtained, Node.js is responsible for processing and filtering the relevant information, ensuring that it is in the desired format for further use.

3. Data Storage: After processing, the refined data is stored in GridDB, a high-performance, scalable NoSQL database, which provides efficient storage and retrieval of the data.

4. Content Tagging with ChatGPT: The processed data is then fed to ChatGPT, a state-of-the-art AI language model. ChatGPT analyzes the data and generates content tags based on the context and relevant keywords.

5. Saving Tags to GridDB: The content tags generated by ChatGPT are subsequently saved back into GridDB, which allows for efficient storage, retrieval, and organization of the tagged content.

6. Presenting the Data to the User with React: After the data has been processed, stored, and tagged, it is important to present it in an accessible and user-friendly format. In this case, React, a popular JavaScript library for building user interfaces, is utilized to create a web-based application that displays news and the tagged content in an organized manner.

In summary, the updated flow diagram represents a system that employs Node.js for data acquisition and processing, GridDB for data storage, ChatGPT for content tagging, and React for creating a user-friendly interface that presents the data to end-users, resulting in a comprehensive and efficient multi-news data handling solution.

## Key Concepts and Practical Codes

### Using ChatGPT to Generate News Tags

ChatGPT is an advanced artificial intelligence language model developed by OpenAI, based on the GPT (Generative Pre-trained Transformer) architecture. The model is designed to understand and generate human-like text based on the input it receives. It is capable of performing various tasks, including answering questions, engaging in conversation, and generating text in a range of styles and formats. ChatGPT has been widely used in various applications, such as chatbots, content generation, translation, and more.

As the core natural language processing component, ChatGPT is responsible for analyzing and generating tags from news articles. By leveraging its advanced language understanding capabilities, ChatGPT can identify and group related articles based on their underlying themes, significantly enhancing content organization and discovery.

#### Prompt

As ChatGPT is capable of natural language understanding, generating tags from a news article is ridiculously easy. All we have to do is ask for it!

A prompt is text used to guide a language model or machine learning system to generate a relevant output. It can take many forms, such as questions, commands, or passages of text, and a well-designed prompt is crucial for accuracy and relevance of the output.

To generate tags from a news article, we can provide a prompt to ChatGPT that specifies the desired output. For example, in JavaScript the prompt could be structured like this:

```js
const prompt = `Generate five tags from this news with each tag is less than 2 words:\n\n${news}`;
```

If we apply that prompt directly into the [ChatGPT](https://chat.openai.com/chat) website with a specific `news` we will get 5 tags.

![generate-tag-chatgpt](/assets/images/tag-generate-chatgpt.png)

OpenAI provide [Chat API](https://platform.openai.com/docs/api-reference/chat) for user to access ChatGPT models. We will use `gpt-3.5-turbo` model to generate tags from our news.

```js
import { Configuration, OpenAIApi } from "openai";

const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY,
});
const openai = new OpenAIApi(configuration);

async function generateTagsFromNews(news) {
  const prompt = `Generate five tags from this news with each tag is less than 2 words:\n\n${news}`;
  try {
    const completion = await openai.createChatCompletion({
      model: "gpt-3.5-turbo",
      messages: [{ role: "user", content: prompt }],
    });
    return completion.data.choices[0].message;
  } catch (error) {
    console.error("Error occurred while generating tags:", error);
    // Handle the error as needed, or return a suitable error message
    return "An error occurred while generating tags.";
  }
}

export { generateTagsFromNews };
```

To access the OpenAI API, you'll need to obtain an `OPENAI_API_KEY`, which can be obtained from this [link](https://platform.openai.com/account/api-keys). Note that you'll need to set up a paid account in order to use the API, but the cost is quite low at just $0.002 per 1,000 tokens. A rough estimate is that 1,000 tokens corresponds to around 200 to 300 words in English, based on the average word length of 4-5 characters plus a space (totaling 5-6 characters per word).
