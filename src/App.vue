<script setup>
import { ref, nextTick } from 'vue'

const inputCommand = ref('')
const history = ref([
  { type: 'output', text: 'Bienvenido a mi CV interactivo. Escribe "help" para ver los comandos disponibles.' }
])
const inputRef = ref(null)

// Objeto con las respuestas de los comandos
const commands = {
  help: 'Comandos disponibles: experience, skills, contact, clear',
  experience: '2021-Presente: Desarrollador Frontend en X...',
  skills: 'Vue 3, Vite, JavaScript, CSS, HTML...',
  contact: 'Email: hola@tuemail.com | GitHub: @tuusuario',
}

const handleCommand = async () => {
  const cmd = inputCommand.value.trim().toLowerCase()
  if (!cmd) return

  // 1. Guardar el comando que escribió el usuario
  history.value.push({ type: 'input', text: `invitado@tu-nombre:~$ ${cmd}` })

  // 2. Procesar el comando
  if (cmd === 'clear') {
    history.value = []
  } else if (commands[cmd]) {
    history.value.push({ type: 'output', text: commands[cmd] })
  } else {
    history.value.push({ type: 'output', text: `Comando no encontrado: ${cmd}. Escribe "help" para ayuda.` })
  }

  // 3. Limpiar input y hacer scroll hacia abajo
  inputCommand.value = ''
  await nextTick()
  window.scrollTo(0, document.body.scrollHeight)
}
</script>

<template>
  <div class="terminal" @click="inputRef.focus()">
    <div v-for="(line, index) in history" :key="index" :class="line.type">
      {{ line.text }}
    </div>
    
    <div class="input-line">
      <span class="prompt">invitado@tu-nombre:~$</span>
      <input 
        ref="inputRef"
        v-model="inputCommand" 
        @keyup.enter="handleCommand"
        autofocus
        spellcheck="false"
      />
    </div>
  </div>
</template>