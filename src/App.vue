<script setup>
    import MyForm from "./components/Home.vue";
    import PrivacyPolicy from "./components/PrivacyPolicy.vue";
    import Contact from "./components/Contact.vue";
    import NotFound from "./components/NotFound.vue";
    import {computed, ref} from "vue";
    import Navigation from "./components/layouts/Navigation.vue";
    import Footer from "./components/layouts/Footer.vue";
    import ErrorDetector from "./components/ErrorDetector.vue";

    const routes = {
        '/': MyForm,
        '/find-errors': ErrorDetector,
        '/privacy': PrivacyPolicy,
        '/contact': Contact
    }

    const currentPath = ref(window.location.hash)

    window.addEventListener('hashchange', () => {
        currentPath.value = window.location.hash
    })

    const currentView = computed(() => {
        return routes[currentPath.value.slice(1) || '/'] || NotFound
    })
</script>

<template>
  <div id="app">
    <Navigation></Navigation>
    <component :is="currentView" />
    <Footer></Footer>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Pixelify+Sans:wght@600&family=Roboto&display=swap');

*{
    font-family: 'Roboto', sans-serif;
}
</style>