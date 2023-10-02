<script setup>
import { ref, computed } from "vue";
import { marked } from "marked";
import prism from "prismjs";

// Add numbering to the Code blocks
import "prismjs/plugins/line-numbers/prism-line-numbers.js";
import "prismjs/plugins/line-numbers/prism-line-numbers.css";

import "prismjs/plugins/toolbar/prism-toolbar.js"; // required for the following plugins
import "prismjs/plugins/toolbar/prism-toolbar.css"; // required for the following plugins
import "prismjs/plugins/copy-to-clipboard/prism-copy-to-clipboard.js"; // show copy button
import "prismjs/plugins/show-language/prism-show-language.js"; // display the language of the code block
import "prismjs/components/prism-python.min.js"
import "prismjs/components/prism-java.min.js"
// import "prismjs/components/prism-cpp.js"

// This is needed for a conflict with other CSS files being used (i.e. Bulma).
import "prismjs/plugins/custom-class/prism-custom-class";
import axios from "axios";
prism.plugins.customClass.map({ number: "prism-number", tag: "prism-tag" });

// sample markdown file
const mdfile = "https://gist.githubusercontent.com/rt2zz/e0a1d6ab2682d2c47746950b84c0b6ee/raw/83b8b4814c3417111b9b9bef86a552608506603e/markdown-sample.md";
const markDown = ref("");
const answer = ref("");

//marked Options => https://marked.js.org/using_advanced#options
marked.use({
    highlight: (code, lang) => {
        if (prism.languages[lang]) {
            return prism.highlight(code, prism.languages[lang], lang);
        } else {
            return code;
        }
    },
});

// read in the md file and apply the highlight style to the Code Block
// const getMarkdownData = async () => {
//     await fetch(mdfile)
//         .then((response) => response.text())
//         .then((data) => (markDown.value = data));
//     prism.highlightAll(); // perform the highlighting of the Code Blocks
// };

function makeOpenAIRequest() {
    axios.post(
        'https://api.openai.com/v1/chat/completions',
        {
            model: 'gpt-3.5-turbo',
            messages: [
                {
                    role: "user",
                    content: `Write  Solution code to House robber  problem in Python . I want the code with proper indentation.`
                }
            ],
            max_tokens: 200,
        },
        {
            headers: {
                'Authorization': `Bearer ${import.meta.env.VITE_OPEN_API_KEY}`,
                'Content-Type': 'application/json',
            },
        }
    )
    .then(response => {
        answer.value = response.data.choices[0].message.content.replace(/```/g, '~~~');
    })
    .then(() => {
        prism.highlightAll(); // Perform the highlighting of the Code Blocks
    })
    .catch(error => {
        throw new Error('Error making OpenAI request: ' + error.message);
    });
}



// const renderedAnswer = computed(() => {
//     console.log('hello');
//     return marked.parse(answer.value);
// });

// use the finialized markdown to HTML code in the template
const mdToHtml = computed(() => {
    return marked.parse(answer.value);
});

// call the function to be ran
makeOpenAIRequest();
</script>

<template>
    <div class="content">
        <!-- Normal Code Blocks -->
        <!-- <div v-html="mdToHtml"></div> -->
        <!-- Add numbering to the Code Blocks -->
        <div class="line-numbers language-markup" v-html="mdToHtml"></div>
    </div>
</template>

<style>
/* Add the prism themes here */
@import "https://cdnjs.cloudflare.com/ajax/libs/prism-themes/1.9.0/prism-one-light.min.css";
</style>