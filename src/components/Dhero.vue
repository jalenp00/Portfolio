<template>
    <div class="terminal-container">
        <div v-for="(message, index) in replyArray" :key="index" class="terminal-output"
        v-html="message">
        </div>
        <div class="terminal-input">
            <label id="label" for="input">{{ userDir }}</label>
            <textarea id="input" rows="1" cols="50" maxlength="50" v-model="user"
            @keydown.enter.prevent="run"></textarea>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

const replyArray = ref<string[]>([]);
const user = ref<string | null>('');
const queue = ref<string[]>([]);
const isTyping = ref(false);

const userDir = 'guest@jalenpownell.com>';
const initialText = ['Welcome to my website!', 'Type "help" for a list of commands.'];
const helpText = [
    'Available commands:',
    '• help - Display this list of commands',
    '• clear - Clear the terminal',
    '• projects - Display projects',
    '• contact - Display contact information',
    '• resume - Display resume',
    '• whoami - About me',
    '• whoareyou - About you'
];

const typeText = async (newText: string, time: number) => {
    isTyping.value = true;
    let currentText = '';

    for (const char of newText) {
        currentText += char;
        replyArray.value[replyArray.value.length - 1] = currentText;
        await new Promise(resolve => setTimeout(resolve, time));
    }

    isTyping.value = false;
    processQueue();
};

const processQueue = async () => {
    if (queue.value.length > 0 && !isTyping.value) {
        const nextText = queue.value.shift();
        if (nextText) {
            replyArray.value.push('');
            if (nextText.includes('<a href=')) {
                await typeText(nextText, 0);
            } else {
                await typeText(nextText, 10);
            }
        }
    }
};

const queueText = (newText: string) => {
    queue.value.push(newText);
    processQueue();
};

onMounted(() => {
    setTimeout(() => {
        for (const text of initialText) {
            queueText(text);
        }
    }, 1000);
});

const run = function () {
    if (user.value == null) {
        return;
    }

    const command: string = user.value;
    user.value = null;

    queueText(userDir + ' ' + command);

    if (command === 'help') {
        for (const text of helpText) {
            queueText(text);
        }
    } else if (command === 'clear') {
        replyArray.value = [];
        queueText('Terminal cleared.');
        queueText('Type "help" for a list of commands.');
    } else if (command === 'projects') {
        queueText('Projects:');
        queueText('• devJobs - A job board for developers');
        queueText('Enter the name of a project for more information.');
    } else if (command === 'contact') {
        queueText('Contact information:');
        queueText('• Email: jalenp2000@gmail.com');
        queueText('• LinkedIn: linkedin.com/in/jalenpownell');
        queueText('• GitHub: github.com/jalenp00');
    } else if (command === 'devJobs') {
        queueText('devJobs is a job board for developers.');
        queueText('• Frontend: Vue.js/Nuxt.js, TypeScript, Redis, Tailwind CSS');
        queueText('• Backend: Node.js, FastAPI (Python), MongoDB');
        queueText('• Features: User authentication, job posting, job searching, and job filtering');
        queueText('• Containerized with Docker');
        queueText('• GitHub: <a href="https://github.com/jalenp00/devJobs" target="_blank">github.com/jalenp00/devJobs</a>');
    } else if (command === 'portfolio') {
        queueText('Portfolio:');
        queueText('• This website was created with Vue.js, TypeScript, and Tailwind CSS');
        queueText('• GitHub: github.com/jalenp00/portfolio');
    } else if (command === 'resume') {
        queueText('Resume:');
        queueText('<span> • Download my resume: <a href="https://docs.google.com/document/d/1_1xmTIV5G1dhqbfL_5mUmJHCNET0o5Xl0gaPM1Htr7E/edit?usp=sharing" target="_blank">resume</a> </span>');
    } else if (command === 'whoami') {
        queueText('About me:');
        queueText('• I am a full-stack developer with a passion for creating web applications.');
        queueText('• I am most familiar with Vue/Nuxt.js, Node.js, MongoDB, Orcacle SQL, Java and Python.');
        queueText('• As a self-starter, I am always looking into new technologies and ways to improve my skills.');
        queueText('• I am looking for opportunities to work on projects that will challenge me and help me grow.');
        queueText('• If your in need of a developer that thrives in fast-paced environments and needs to wear multiple hats, this could be a great fit!');
    } else if (command === 'whoareyou') {
        queueText('I was hoping you could tell me, connect with me!');
    } else {
        queueText('Command not found: ' + command);
        queueText('Type "help" for a list of commands.');
    }
};
</script>

<style scoped>
.terminal-container {
    width: 100vw;
    height: 100vh;
    background-color: black;
    position: absolute;
}
.terminal-output {
    color: white;
    font-family: 'VT323', monospace;
    padding-left: 10px;
    font-size: 1.25em;
}
.terminal-input {
    display: flex;
    flex-direction: row;
    align-items: center;
    padding: 10px;
    height: 1.25em;
}

.terminal-reply a {
    color: blue; /* Ensure the link is styled correctly */
    text-decoration: underline; /* Ensure the link is underlined */
}

#label {
    font-size: 1.25em;
    color: white;
    padding: 5px;
}

#input {
    flex: 1;
    background-color: black;
    color: white;
    border: none;
    outline: none;
    resize: none;
    font-size: 1.25em;
    padding: 5px;
    margin-left: 5px;
}
</style>