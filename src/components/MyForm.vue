<script setup>
import {computed, ref} from 'vue';
import axios from 'axios';
import {marked} from "marked";
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
import Navigation from "./Navigation.vue";

prism.plugins.customClass.map({ number: "prism-number", tag: "prism-tag" });


// Define reactive variables
    const problem = ref('');
    const answer = ref('');
    const answerExplain = ref('');
    const description = ref('');
    const selectedOption = ref('Python');
    const selectedApproach = ref('Optimized');
    const submitted = ref(false);
    const loading = ref(false);

    marked.use({
        highlight: (code, lang) => {
            if (prism.languages[lang]) {
                return prism.highlight(code, prism.languages[lang], lang);
            } else {
                return code;
            }
        },
    });

    // Define the submitForm function
    const submitForm = async () => {
        try {
            loading.value = true; // Show loading button
            await makeOpenAIRequest(); // Make API request
            // const codeAnswer = await makeOpenAIRequest(); // Make API request
            // const explainAnswer = await makeOpenAIRequestToGetExplain(codeAnswer);
            // answer.value = codeAnswer;
            // prism.highlightAll();

            // answerExplain.value = explainAnswer;
            // console.log('Generated Text:', generatedText); // Handle the generated text
        } catch (error) {
            console.error('Error:', error);
            // Handle error here
        } finally {
            loading.value = false; // Hide loading button
            submitted.value = true; // Show the new section after form submission
        }
    };
    const renderedAnswer = computed(() => {
        console.log('hello');
        return marked.parse(answer.value);
    });

    // Define the makeOpenAIRequest function
    function makeOpenAIRequest() {
        loading.value = true; // Show loading button
        const userPrompt = `Write Solution code to ${problem.value}  problem in ${selectedOption.value} in ${selectedApproach.value} approach . I want the code and explain each line with comments. Explain nothing outside comments. Use classname as Solution. Only provide the code. Before providing the code, just say: "Here's your code:". Don't explain the code and approach`;
        axios.post(
            'https://api.openai.com/v1/chat/completions',
            {
                model: 'gpt-3.5-turbo',
                messages: [
                    {
                        role: "user",
                        content: userPrompt + `
                            ###
                            outline: Write Solution code to HouseRobber II problem in Python in optimized approach . I want the code with proper indentation and explain each line with comments. Use classname as Solution. I only need the code
                            message: Here's your code:
                                Solution code
                            ###
                            outline: Write Solution code to Reverse Words in a String III problem in Java in brute force approach . I want the code with proper indentation and explain each line with comments. Use classname as Solution. I only need the code
                            message: Here's your code:
                                Solution code
                            outline: ${userPrompt},
                            message:
                        `
                    }
                ],
                max_tokens: 700,
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
        .finally(() => {
            loading.value = false; // Hide loading button
            submitted.value = true; // Show the new section after form submission
        })
        .catch(error => {
            throw new Error('Error making OpenAI request: ' + error.message);
        });
    }


    const makeOpenAIRequestToGetExplain = async (code) => {
        try {
            const response = await axios.post(
                'https://api.openai.com/v1/chat/completions',
                {
                    model: 'gpt-3.5-turbo',
                    messages: [
                        {
                            role: "user", content: `Explain this code in points, Code: ${problem.value} in points with spacing between points.`
                        }
                    ],
                    max_tokens: 400,
                },
                {
                    headers: {
                        'Authorization': `Bearer ${import.meta.env.VITE_OPEN_API_KEY}`,
                        'Content-Type': 'application/json',
                    },
                }
            );
            return marked(response.data.choices[0].message.content);
        } catch (error) {
            throw new Error('Error making OpenAI request: ' + error.message);
        }
    };


</script>
<template>
    <div>
        <!-- Form Row -->
        <div class="container mt-8 flex flex-wrap justify-center">
            <h2 class="text-2xl font-bold text-center mb-4">Submit a Problem</h2>
            <div class="w-full p-8 bg-white shadow-lg rounded-lg flex md:flex-row md:space-x-4">
                <div class="flex flex-grow align-text-bottom justify-end">
                    <!-- Form Content -->
                    <div class="">
                        <label for="problem" class="block text-gray-700 text-sm font-bold mb-2">Please enter your problem </label>
                        <input type="text" id="problem" v-model="problem" name="problem" class="w-full border py-2 px-3 rounded-lg focus:outline-none focus:ring focus:border-blue-300" required="">
                    </div>
                </div>

                <div class="flex flex-grow align-top content-start">
                    <div class="w-full">
                        <label for="dropdown" class="block text-gray-700 text-sm font-bold mb-2"> Language </label>
                        <select id="dropdown" v-model="selectedOption" name="dropdown" class="w-full border py-2 px-3 rounded-lg focus:outline-none focus:ring focus:border-blue-300">
                            <option value="Python">Python</option>
                            <option value="Java">Java</option>
                            <option value="C++">C++</option>
                        </select>
                    </div>
                </div>

                <div class="flex flex-grow align-top content-start">
                    <div class="w-full">
                        <label for="dropdown" class="block text-gray-700 text-sm font-bold mb-2"> Approach </label>
                        <select id="dropdown" v-model="selectedApproach" name="dropdown" class="w-full border py-2 px-3 rounded-lg focus:outline-none focus:ring focus:border-blue-300">
                            <option value="Optimized">Optimized</option>
                            <option value="Brute Force">Brute Force</option>
                        </select>
                    </div>
                </div>

                <div class="flex flex-grow">
                    <div class="mt-6 text-center">
                        <button type="button" v-if="!loading" class="bg-red-400 hover:bg-red-500 text-white py-2 px-4 rounded focus:outline-none focus:shadow-outline-red" @click="makeOpenAIRequest"> Search </button>
                        <button type="button" v-if="loading"  class="bg-red-400 hover:bg-red-500 text-white py-2 px-4 rounded focus:outline-none focus:shadow-outline-red" disabled> Loading... </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- After Form Submission Section -->

        <div class="container mx-auto mt-8 flex justify-center">
            <div class="w-full md:mx-32 flex flex-wrap">
<!--                <div class="w-full p-4 bg-white shadow-lg rounded-lg">-->
<!--                    <h2 class="text-2xl font-bold text-center mb-4">Column 1</h2>-->
<!--                    <textarea-->
<!--                        v-model="answer"-->
<!--                        class="w-full h-48 border py-2 px-3 rounded-lg focus:outline-none focus:ring focus:border-blue-300"-->
<!--                        placeholder="Column 1 Content"-->
<!--                    ></textarea>-->
<!--                </div>-->
<!--                <Tiptap></Tiptap>-->

                <div class="line-numbers language-markup w-full" v-html="renderedAnswer" v-if="answer"></div>
<!--                <div v-if="answer" v-html="answer" class="language-* md:w-1/2"></div>-->
<!--                <div v-if="markeds" v-html="markeds" class="language-* md:w-1/2"></div>-->
<!--                <div  class="language-* md:w-1/2"></div>-->
<!--                <div class="w-full md:w-1/2 p-4 bg-white shadow-lg rounded-lg mt-4 md:mt-0" v-if="false">-->
<!--                    <h2 class="text-2xl font-bold text-center mb-4">Column 2</h2>-->
<!--                    <textarea-->
<!--                        v-model="answerExplain"-->
<!--                        class="w-full h-48 border py-2 px-3 rounded-lg focus:outline-none focus:ring focus:border-blue-300"-->
<!--                        placeholder="Column 2 Content"-->
<!--                    ></textarea>-->
<!--                </div>-->
            </div>
        </div>

        <!-- What to Do Section -->
        <div class="bg-gray-100 py-16 text-center">
            <div class="reasons-section">
                <h2 class="text-3xl  text-gray-950 mb-8 text-center">Why should you try Aigorithms?</h2>

                <div class="columns-container">
                    <div class="column">
                        <div class="icon">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v12m-3-2.818l.879.659c1.171.879 3.07.879 4.242 0 1.172-.879 1.172-2.303 0-3.182C13.536 12.219 12.768 12 12 12c-.725 0-1.45-.22-2.003-.659-1.106-.879-1.106-2.303 0-3.182s2.9-.879 4.006 0l.415.33M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                        </div>

                        <h3 class="text-xl mb-4 text-gray-800">FREE OF CHARGE</h3>
                        <p class="text-gray-600">
                            Unlimited answers to leetcode (or any other coding questions) are available at any time.
                            The Aigorithms works for you 24/7 without compulsory registration.
                        </p>
                    </div>

                    <div class="column">
                        <div class="icon">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M12 18v-5.25m0 0a6.01 6.01 0 001.5-.189m-1.5.189a6.01 6.01 0 01-1.5-.189m3.75 7.478a12.06 12.06 0 01-4.5 0m3.75 2.383a14.406 14.406 0 01-3 0M14.25 18v-.192c0-.983.658-1.823 1.508-2.316a7.5 7.5 0 10-7.517 0c.85.493 1.509 1.333 1.509 2.316V18" />
                            </svg>
                        </div>
                        <h3 class="text-xl mb-4 text-gray-800">TECHNOLOGY</h3>
                        <p class="text-gray-600">
                            Our AI uses natural language processing to grab critical information while maintaining the original context.
                        </p>
                    </div>

                    <div class="column">
                        <div class="icon">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 14.25v-2.625a3.375 3.375 0 00-3.375-3.375h-1.5A1.125 1.125 0 0113.5 7.125v-1.5a3.375 3.375 0 00-3.375-3.375H8.25M9 16.5v.75m3-3v3M15 12v5.25m-4.5-15H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 00-9-9z" />
                            </svg>

                        </div>
                        <h3 class="text-xl mb-4 text-gray-800">TOTAL SAFETY</h3>
                        <p class="text-gray-600">
                            The texts you check are not saved in the system or used anywhere for the purposes of third parties.
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>



<style>
/* Add the prism themes here */
@import "https://cdnjs.cloudflare.com/ajax/libs/prism-themes/1.9.0/prism-one-light.min.css";

.reasons-section {
    padding: 50px 0;
    text-align: center;
}

.columns-container {
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
}

.column {
    max-width: 300px;
    margin: 20px;
}

.icon svg {
    @apply bg-red-400;
    color: white;
    border-radius: 50%;
    min-width: 80px;
    padding: 15px 2px;
    text-align: center;
    min-height: 80px;
    margin: 0 auto 10px;
}
</style>