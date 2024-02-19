<template>
  <div class="chat-container">
    <div v-if="welcomeMessageShown" class="welcome-message">
      <img class="logo" src="../assets/ChatGpt.png" alt="Logo" />
      <p>¿Cómo puedo ayudarte hoy?</p>
    </div>

    <div class="chat-messages">
      <div
        v-for="(message, index) in chatMessages"
        :key="index"
        class="message">
        <div v-if="message.role === 'user'" class="user-message">
          <strong class="user-name">TU:</strong>
          <span class="user-text">{{ message.content }}</span>
        </div>
        <div v-else-if="message.role === 'bot'" class="bot-message">
          <strong class="bot-name">Chat GPT:</strong>
          <span class="bot-text">{{ message.content }}</span>
        </div>
      </div>
    </div>
    <div class="chat-input">
      <input
        type="text"
        v-model="inputMessage"
        @keyup.enter="sendMessage"
        placeholder="Mensaje ChatGPT…"
        class="message-input" />
      <button @click="sendMessage" class="send-button">
        <span>
          <svg
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg">
            <path
              d="M7 11L12 6L17 11M12 18V7"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round" />
          </svg>
        </span>
      </button>
    </div>
    <div
      v-if="loading"
      id="lottie-animation"
      class="lottie-animation"
    ></div>
  </div>
</template>

<script>
import Lottie from "lottie-web";
import animationData from "../assets/loading.json"; // Reemplaza esto con la ubicación de tu archivo JSON de animación
import axios from "axios";

export default {
  data() {
    return {
      inputMessage: "",
      chatMessages: [],
      welcomeMessageShown: true,
      loading: false, // Agrega un estado para controlar la visibilidad de la animación de carga
    };
  },
  methods: {
    async sendMessage() {
      if (this.inputMessage.trim() === "") return;

      this.welcomeMessageShown = false;
      this.loading = true; // Muestra la animación de carga al enviar un mensaje

      try {
        // Enviar mensaje del usuario
        this.chatMessages.push({ role: "user", content: this.inputMessage });

        const response = await axios.post(
          "https://chatgpt-42.p.rapidapi.com/conversationgpt4",
          {
            messages: [{ role: "user", content: this.inputMessage }],
            max_tokens: 2500,
            language: "es",
          },
          {
            headers: {
              "content-type": "application/json",
              "X-RapidAPI-Key":
                "ba92110fa7msh49d75321278541bp102efcjsndfb1e40e0c06",
              "X-RapidAPI-Host": "chatgpt-42.p.rapidapi.com",
            },
          }
        );

        // Agregar respuesta de ChatGPT
        this.chatMessages.push({ role: "bot", content: response.data.result });
      } catch (error) {
        console.error("Error al enviar mensaje:", error);
      } finally {
        this.loading = false; // Oculta la animación de carga después de recibir la respuesta
      }

      this.inputMessage = ""; // Limpiar el campo de entrada después de enviar el mensaje
    },
  },
  mounted() {
    // Inicializa la animación Lottie cuando el componente se monta
    Lottie.loadAnimation({
      container: document.querySelector(".lottie-animation"),
      renderer: "svg",
      loop: true,
      autoplay: false, // Desactiva la reproducción automática inicial
      animationData: animationData, // Reemplaza esto con la variable que contiene los datos de tu animación
    });
  },
};
</script>

<style scoped>

.lottie-animation {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5); /* Fondo oscuro semitransparente */
  z-index: 9999; /* Posición encima de todo */
}



.chat-container {
  width: 65vw; /* Ancho completo de la ventana */
  height: 100vh; /* Altura completa de la ventana */
  display: flex;
  flex-direction: column;
  padding-left: 30px;
  padding-right: -30px;
}

.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 50px;
}

.message {
  margin-bottom: 20px;
}

.user-message {
  text-align: right;
  color: #ffffff; /* Color del texto del mensaje del usuario */
  padding-right: 30px;
}

.bot-message {
  text-align: left;
  color: #ffffff;
}

.text {
  background-color: #424242; /* Color de fondo del mensaje del bot */
  padding: 10px;
  border-radius: 10px; /* Bordes redondeados */
}

.chat-input {
  display: flex;
  padding: 20px;
}

.chat-input input {
  flex: 1;
  padding: 10px;
  border-radius: 12px;
  border: 1px solid #7a7a7a;
  background-color: #181818;
}

.chat-input button {
  padding: 8px 12px;
  margin-left: 10px;
  border: none;
  border-radius: 12px;
  background-color: #007bff;
  color: #fff;
  cursor: pointer;
}

.chat-input button:hover {
  background-color: #0056b3;
}

.message-input {
  color: #fff;
}

.message-input::placeholder {
  color: #5c5c5c;
  margin-right: 30px;
  font-size: 16px;
  font-weight: 500;
  font-family: "Poppins";
}
.welcome-message {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
  font-size: 30px;
  font-family: "Poppins";
}

.logo {
  width: 100px;
  height: 100px;
  margin-bottom: 20px;
  /* border-radius: 400px; */
}

.user-text {
  color: #ffffff;
  background-color: #007bff; /* Color de fondo del mensaje del usuario */
  padding: 10px;
  border-radius: 10px;
}

.user-name,
.bot-name {
  font-weight: bold;
  display: block;
  margin-bottom: 10px;
}

@media (max-width: 768px) {
  .chat-container {
    width: 90vw;
  }

  .avatar,
  .user {
    width: 40px;
    height: 40px;
  }

  .message {
    margin-bottom: 25px;
  }

  .chat-input {
    padding: 15px;
  }

  .chat-input input {
    padding: 8px;
  }

  .chat-input button {
    padding: 6px 8px;
  }

  .welcome-message {
    font-size: 24px;
  }

  .logo {
    width: 80px;
    height: 80px;
  }
}

/* Estilos para pantallas de móvil */
@media (max-width: 480px) {
  .chat-container {
    width: 80vw;
    height: 850px;
  }

  .bot-message {
    margin-left: -50px;
    margin-bottom: 20px;
  }

  .message {
    margin-bottom: 10px;
  }
  .bot-text {
    padding: 10px; /* Ajustar el relleno */
    border-radius: 10px; /* Bordes redondeados */
    max-width: 70%; /* Limitar el ancho del mensaje para evitar que se extienda demasiado */
  }

  .chat-input {
    padding: 10px;
    margin-left: -30px;
  }

  .chat-input input {
    padding: 6px;
  }

  .chat-input button {
    padding: 5px 6px;
    margin-right: 30px;
  }

  .welcome-message {
    font-size: 24px;
  }

  .logo {
    width: 60px;
    height: 60px;
  }
}
</style>
