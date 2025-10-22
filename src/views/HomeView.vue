<script setup lang="ts">
import { ref } from 'vue'
import AppDrawer from '@/components/Drawer.vue'
import ContactForm from '@/components/ContactForm.vue'

const isDrawerOpen = ref(false)

// HomeView delegates form UI/logic to ContactForm component

const openDrawer = () => {
  isDrawerOpen.value = true
}

const closeDrawer = () => {
  isDrawerOpen.value = false
}

// Form submission is handled inside ContactForm.vue
</script>

<template>
  <div class="home-page">
    <div class="content">
      <button class="contact-btn font-sans" @click="openDrawer">CONTACT</button>
    </div>

    <AppDrawer :is-open="isDrawerOpen" @close="closeDrawer">
      <ContactForm />
    </AppDrawer>
  </div>
</template>

<style scoped>
.home-page {
  height: 100vh;
}

.content {
  margin-inline: 30px;
  margin-top: 20px;
  display: flex;
  justify-content: end;
  align-items: start;
}
</style>

<style>
.contact-btn {
  font-weight: 700;
  font-size: 16px;
  line-height: 110%;
  letter-spacing: 1%;
  text-transform: uppercase;
  background-color: transparent;
  outline: none;
  border: 2px solid #00ff6a;
  padding-block: 4px;
  padding-inline: 8px;
  border-radius: 8px;
  color: #00ff6a;
  cursor: pointer;
  transition:
    background-color 0.2s ease-in-out,
    color 0.2s ease-in-out;
}

.contact-btn:hover {
  background-color: #00ff6a;
  color: #000000;
}
</style>
