# Vue.js Frontend para Laravel Reverb

Este projeto frontend é uma interface construída com Vue.js para enviar e escutar emojis em tempo real usando o Laravel Reverb.

## Pré-requisitos

Antes de iniciar, verifique se você possui as seguintes dependências:

- Node.js (versão 16 ou superior)
- Vue 3.x
- Vite como bundler
- Axios para comunicação com o backend
- Laravel Echo e Pusher para comunicação em tempo real via WebSockets

## Passo a Passo para Configuração

### 1. Instalar Dependências

Primeiro, instale as dependências do projeto frontend com o npm ou yarn.

npm install

### 2. Configurar as Variáveis de Ambiente
Crie um arquivo .env no diretório raiz do seu projeto Vue.js (se ainda não existir). Adicione as variáveis de ambiente geradas no backend Laravel para o Reverb, conforme abaixo:

REVERB_APP_ID=
REVERB_APP_KEY=
REVERB_APP_SECRET=
REVERB_HOST=
REVERB_PORT=
REVERB_SCHEME=

VITE_REVERB_APP_KEY="${REVERB_APP_KEY}"
VITE_REVERB_HOST="${REVERB_HOST}"
VITE_REVERB_PORT="${REVERB_PORT}"
VITE_REVERB_SCHEME="${REVERB_SCHEME}"

### 3. Instalar as Dependências do Laravel Echo e Pusher
No frontend, para se comunicar com o servidor Reverb via WebSocket, instale as bibliotecas Laravel Echo e Pusher:

npm install --save laravel-echo pusher-js


### 4. Configuração do Echo e Pusher
Agora, configure o Laravel Echo com o Reverb e Pusher. O código abaixo deve ser colocado no seu arquivo main.js ou main.ts, que inicia o aplicativo Vue.js:

import './assets/main.css';

import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import Echo from 'laravel-echo';
import Pusher from 'pusher-js';
import axios from 'axios';

window.Pusher = Pusher;

window.Echo = new Echo({
  broadcaster: 'reverb',
  key: import.meta.env.VITE_REVERB_APP_KEY,
  wsHost: import.meta.env.VITE_REVERB_HOST,
  wsPort: import.meta.env.VITE_REVERB_PORT ?? 80,
  wssPort: import.meta.env.VITE_REVERB_PORT ?? 443,
  forceTLS: (import.meta.env.VITE_REVERB_SCHEME ?? 'https') === 'https',
  enabledTransports: ['ws', 'wss'],
});

const app = createApp(App);
app.use(router);
app.mount('#app');
