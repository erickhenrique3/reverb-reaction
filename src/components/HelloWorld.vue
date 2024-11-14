<template>
  <div class="emoji-dashboard">
    <div class="emoji-container">
      <span class="emoji" @click="sendEmoji('🔥')">🔥</span>
      <span class="emoji" @click="sendEmoji('🚀')">🚀</span>
      <span class="emoji" @click="sendEmoji('💚')">💚</span>
    </div>
    <div v-for="(emoji, index) in emojis" :key="index" class="emoji-float" :style="{ top: emoji.y + 'px', left: emoji.x + 'px' }">
      {{ emoji.symbol }}
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'; 
import axios from 'axios';


export default {
  name: 'HelloWorld', 
  setup() {
    const emojis = ref([]);

    const sendEmoji = (emoji) => {
      axios.post('http://127.0.0.1:8000/api/emoji', { emoji })
        .then(() => addFloatingEmoji(emoji))
        .catch(error => console.error("Erro ao enviar emoji:", error));
      reaction();
    };

    const reaction = () => {
      window.Echo.channel('emoji-reaction')
        .listen('EmojiReceived', (event) => {
          console.log(event);
          addFloatingEmoji(event.emoji);
        });
    };

    const addFloatingEmoji = (emoji) => {
      const x = Math.random() * window.innerWidth;
      const y = window.innerHeight - 100;

      emojis.value.push({ symbol: emoji, x, y });

      setTimeout(() => {
        emojis.value.shift();
      }, 2000);
    };

    onMounted(() => {
      reaction();
    });

    return { emojis, sendEmoji };
  }
};
</script>

<style scoped>
/* Layout Geral */
body {
  background: linear-gradient(145deg, #141e30, #243b55);
  margin: 0;
  overflow: hidden;
  font-family: "Arial", sans-serif;
}

/* Dashboard principal */
.emoji-dashboard {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.emoji-container {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  padding: 20px;
  display: flex;
  gap: 20px;
  box-shadow: 0px 4px 20px rgba(0, 0, 0, 0.4);
  backdrop-filter: blur(10px);
}

.emoji {
  font-size: 3rem;
  cursor: pointer;
  color: #ffffff;
  transition: transform 0.3s, color 0.3s;
}

.emoji:hover {
  transform: scale(1.5);
  color: #ffcc00;
}

/* Emojis flutuantes */
.emoji-float {
  position: absolute;
  font-size: 2.5rem;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.8);
  animation: rocketUp infinite 3s linear;
}

/* Animação de foguete */
@keyframes rocketUp {
  0% {
    opacity: 1;
    transform: translateY(0);
  }
  100% {
    opacity: 0;
    transform: translateY(-150vh);
  }
}
</style>