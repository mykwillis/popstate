<template>
  <div>
    <h1>Vue 3 History and popstate Example</h1>
    <div v-if="isModalOpen" class="modal">
      <div class="modal-content">
        <h2>Modal Dialog</h2>
        <p>You opened this modal before navigating to a new site.</p>
        <a :href="externalLink" @click="handleExternalLinkClick"
          >Go to External Site</a
        >
        <button @click="closeModal">Close Modal</button>
      </div>
    </div>
    <button @click="openModal">Open Modal</button>
    <a :href="externalLink" @click="handleExternalLinkClick"
      >Go to External Site</a
    >
  </div>
</template>

<script>
import { onMounted, onUnmounted, ref } from "vue";

export default {
  setup() {
    const isModalOpen = ref(false);

    const openModal = () => {
      isModalOpen.value = true;
      window.history.pushState({ modalOpen: true }, "", "");
    };

    const closeModal = () => {
      isModalOpen.value = false;
      window.history.back();
    };

    const handlePopState = (event) => {
      // Restore modal state on popstate
      if (event.state?.modalOpen) {
        isModalOpen.value = true;
      } else {
        isModalOpen.value = false;
      }
    };

    const handleLoad = () => {
      if (window.history.state?.modalOpen) {
        isModalOpen.value = true;
      } else {
        isModalOpen.value = false;
      }
    };

    const handleExternalLinkClick = (event) => {
      window.history.pushState({ modalOpen: isModalOpen.value }, "", "");
    };

    onMounted(() => {
      window.addEventListener("popstate", handlePopState);
      window.addEventListener("load", handleLoad);
    });

    onUnmounted(() => {
      window.removeEventListener("popstate", handlePopState);
      window.removeEventListener("load", handleLoad);
    });

    return {
      isModalOpen,
      openModal,
      closeModal,
      handleExternalLinkClick,
      externalLink: "https://www.example.com",
    };
  },
};
</script>

<style>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
</style>
