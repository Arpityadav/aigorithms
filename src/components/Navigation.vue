<script setup>

</script>

<template>
    <header class="h-20 bg-white">
        <nav class="relative px-2 py-4">

            <div class="container mx-auto flex justify-between items-center">
                <!--                    <img src="https://avrasys.hu/logoipsum-logo-54.svg" alt="Tailwindcss Navigation">-->
                <p class="font-semibold text-red-400 text-4xl ml-4" style="font-family: 'Pixelify Sans', cursive;">Aigorithms</p>

                <ul class="hidden md:flex space-x-6">
                    <li><a href="#">Home</a></li>
<!--                    <li><a href="#/contact">Contact</a></li>-->
                    <li><a href="#/privacy">Privacy</a></li>
                </ul>

                <!--                    <a href="#" class="bg-red-400 px-5 py-1 rounded-3xl hover:bg-red-500 text-white hidden md:flex" role="button">Sign In</a>-->

                <!-- Mobile menu icon -->
                <button id="mobile-icon" class="md:hidden">
                    <i onclick="changeIcon(this)" class="fa-solid fa-bars"></i>
                </button>

            </div>

            <!-- Mobile menu -->
            <div class="md:hidden flex justify-center mt-3 w-full">
                <div id="mobile-menu" class="mobile-menu absolute top-23 w-full"> <!-- add hidden here later -->
                    <ul class="bg-gray-100 shadow-lg leading-9 font-bold h-screen">
                        <li class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="https://google.com" class="block pl-7">Home</a></li>
                        <li class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="#" class="block pl-7">News</a></li>
                        <li class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="#" class="block pl-7">About</a></li>
                        <li class="border-b-2 border-white hover:bg-red-400 hover:text-white pl-4"><a href="#" class="block pl-7">Contact</a></li>
                    </ul>
                </div>
            </div>

        </nav>
    </header>
</template>

<style scoped>

</style>