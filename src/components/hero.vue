<template>
    <div class="w-[100vw] flex-row overflow-auto pl-[2%]"
    :class="darkMode ? 'bg-black text-white' : 'bg-[#f1f2f1] text-black'"
    ref="container">
        <div v-for="(message, index) in replyArray" :key="index" 
        class="text-[1.25em]"
        v-html="message">
        </div>
        <div class="text-[1.25em]"
        :class="darkMode ? 'text-white' : 'text-black'">
            <label id="label" for="input" class="align-middle">{{ userDir }}</label>
            <textarea
            id="input" rows="1" cols="50" maxlength="50"
            v-model="user"
            class="bg-transparent border-none overflow-hidden resize-none focus:outline-none focus:ring-0 align-middle custom-block-cursor"
            @keydown.enter.prevent="run"
            @keydown.up="handleKey('up')"
            @keydown.down="handleKey('down')"
            ref="inputTextarea"
            ></textarea>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUpdated, nextTick } from 'vue';

const replyArray = ref<string[]>([]);
const user = ref<string | null>('');
const userHistory = ref<string[]>([]);
const userIndex = ref<number>(0);
const queue = ref<string[]>([]);
const isTyping = ref(false);
const inputTextarea = ref<HTMLTextAreaElement | null>(null);
const container = ref<HTMLDivElement | null>(null);
const darkMode = ref<boolean>(true);
const asciiArt = [
  `<pre>
     _       _            _       _____                   _             _ 
    | | __ _| | ___ _ __ ( )___  |_   _|__ _ __ _ __ ___ (_)_ __   __ _| |
 _  | |/ _  | |/ _ \\ '_ \\|// __|   | |/ _ \\ '__| '_ \` _ \\| | '_ \\ / _\` | |
| |_| | (_| | |  __/ | | | \\__ \\   | |  __/ |  | | | | | | | | | | (_| | |
 \\___/ \\__,_|_|\\___|_| |_| |___/   |_|\\___|_|  |_| |_| |_|_|_| |_|\\__,_|_|
  </pre>`
];
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
    '• whoareyou - About you',
    `• mode - 'mode dark' or 'mode light'`

];

const links = {
    resume: '<a href="https://docs.google.com/document/d/1_1xmTIV5G1dhqbfL_5mUmJHCNET0o5Xl0gaPM1Htr7E/edit?usp=sharing" target="_blank" class="text-red-500 underline">resume</a>',
    linkedin: '<a href="https://www.linkedin.com/in/jalenpownell" target="_blank" class="text-red-500 underline">jalenpownell</a>',
    github: '<a href="https://github.com/jalenp00/" target="_blank" class="text-red-500 underline">jalenp00</a>',
    email: '<a>jalenp2000@gmail.com</a>',
    devJobs: '<a href="https://github.com/jalenp00/devJobs" target="_blank" class="text-red-500">devJobs</a>',
    portfolio: '<a href="https://github.com/jalenp00/portfolio" target="_blank" class="text-red-500">portfolio</a>'
};

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
        for (const text of asciiArt) {
            queueText(text);
        }
        for (const text of initialText) {
            queueText(text);
        }
    }, 500);
    userHistory.value.push('');

    if (inputTextarea.value) {
        inputTextarea.value.focus();
    }
});

onUpdated(() => {
    nextTick(() => {
        scrollToBottom()
    })
});

const handleKeyDown = () => {
    if (userIndex.value === 0) {
        return;
    }
    userIndex.value -= 1;
    if (userIndex.value >= 0 && userHistory.value.length >= userIndex.value) {
        user.value = userHistory.value[userHistory.value.length - userIndex.value];
    }
};
const handleKeyUp = () => {
    if (userIndex.value === userHistory.value.length-1) {
        return;
    }
    userIndex.value += 1;
    if (userIndex.value >= 0 && userHistory.value.length >= userIndex.value) {
        user.value = userHistory.value[userHistory.value.length - userIndex.value];
    }
};

const handleKey = (key: string) => {
    if (key === 'down') {
        handleKeyDown();
    } else if (key === 'up') {
        handleKeyUp();
    }
    
};

const scrollToBottom = () => {
    if (container.value) {
        container.value.scrollTop = container.value.scrollHeight
    }
}

const run = function () {
    if (user.value == null) {
        return;
    }

    const command: string = user.value;
    userHistory.value.push(command);
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
        queueText('• Email: ' + links.email);
        queueText('• LinkedIn: ' + links.linkedin);
        queueText('• GitHub: ' + links.github);
        queueText('• Resume: ' + links.resume);
    } else if (command === 'devJobs') {
        queueText('devJobs is a job board for developers.');
        queueText('• Frontend: Vue.js/Nuxt.js, TypeScript, Redis, Tailwind CSS');
        queueText('• Backend: Node.js, FastAPI (Python), MongoDB');
        queueText('• Features: User authentication, job posting, job searching, and job filtering');
        queueText('• Containerized with Docker');
        queueText('• GitHub: ' + links.devJobs);
    } else if (command === 'portfolio') {
        queueText('Portfolio:');
        queueText('• This website was created with Vue.js, TypeScript, and Tailwind CSS');
        queueText('• GitHub: ' + links.portfolio);
    } else if (command === 'resume') {
        queueText('Resume:');
        queueText('<span> • Download my resume: ' + links.resume);
    } else if (command === 'whoami') {
        queueText('About me:');
        queueText('• I am a full-stack developer with a passion for creating web applications.');
        queueText('• I am most familiar with Vue/Nuxt.js, Node.js, MongoDB, Orcacle SQL, Java and Python.');
        queueText('• As a self-starter, I am always looking into new technologies and ways to improve my skills.');
        queueText('• I am looking for opportunities to work on projects that will challenge me and help me grow.');
        queueText('• If your in need of a developer that thrives in fast-paced environments and needs to wear multiple hats, this could be a great fit!');
    } else if (command === 'whoareyou') {
        queueText('I was hoping you could tell me, connect with me!');
    } else if (command === 'mode dark') {
        darkMode.value = true;
    } else if (command === 'mode light') {
        darkMode.value = false;

    }else {
        queueText('Command not found: ' + command);
        queueText('Type "help" for a list of commands.');
    }
};
</script>

<style scoped>

</style>