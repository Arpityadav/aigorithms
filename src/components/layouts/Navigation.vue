<script setup>
import {ref, watch} from 'vue';

const menuActive = ref(false);

function openCloseMenu() {
    menuActive.value = !menuActive.value;
}

// Watch for changes in menuActive
import { onMounted, onBeforeUnmount } from 'vue';

onMounted(() => {
    const hamburger_icon = document.querySelector("#mobile-icon svg");

    function changeIcon() {
        hamburger_icon.classList.toggle("fa-xmark", menuActive.value);
    }

    // Attach the changeIcon function to the menuActive ref
    watch(() => menuActive.value, changeIcon);

    // Define a cleanup function to remove the event listener
    onBeforeUnmount(() => {
        hamburger_icon.removeEventListener('click', openCloseMenu);
    });
});
</script>

<template>
    <header class="h-20 bg-white">
        <nav class="relative px-2 py-4">

            <div class="container mx-auto flex justify-between items-center">
                <a href="#" class="font-semibold text-red-400 text-4xl ml-4 cursor-pointer" style="font-family: 'Pixelify Sans', cursive;">Aigorithms</a>

                <ul class="hidden md:flex space-x-6">
                    <li><a href="#">Home</a></li>
                    <li><a href="#/find-errors">Find Errors in your problem</a></li>
                    <li><a href="https://github.com/Arpityadav/aigorithms/" target="_blank">Github</a></li>
                    <li><a href="#/privacy">Privacy</a></li>
                </ul>

                <button id="mobile-icon" class="md:hidden" @click="openCloseMenu">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 5.25h16.5m-16.5 4.5h16.5m-16.5 4.5h16.5m-16.5 4.5h16.5" />
                    </svg>
                </button>

            </div>

            <div class="md:hidden flex justify-center mt-3 w-full">
                <div id="mobile-menu" :class="{'block': menuActive, 'active': menuActive}" class="mobile-menu absolute top-23 w-full">
                    <ul class="bg-gray-100 shadow-lg leading-9 font-bold h-screen">
                        <li @click="menuActive = false" class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="#" class="block pl-7">Home</a></li>
                        <li @click="menuActive = false" class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="#/find-errors" class="block pl-7">Find errors in your DSA problem</a></li>
                        <li @click="menuActive = false" class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="https://github.com/Arpityadav/aigorithms/" target="_blank" class="block pl-7">Github</a></li>
                        <li @click="menuActive = false" class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="#/privacy" class="block pl-7">Privacy</a></li>
                    </ul>
                </div>
            </div>

        </nav>
    </header>
</template>

<style scoped>
    .mobile-menu {
        left: -200%;
        transition: 0.5s;
    }

    .mobile-menu.active {
        left: 0;
    }

    .mobile-menu ul li ul {
        display: none;
    }

    .mobile-menu ul li:hover ul {
        display: block;
    }
</style>