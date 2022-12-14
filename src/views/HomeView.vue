<script setup>
import { onMounted } from "vue";

onMounted(async () => {
    try {
      await liff.init({ liffId: "1657732539-zn43wJXG" }); // Use own liffId
      if (!liff.isLoggedIn())
        liff.login({ redirectUri: window.location.href });
    } catch (err) {
      console.log(`liff.state init error ${err}`);
    }
  })
</script>

<template>
  <div>HELLo</div>
</template>
