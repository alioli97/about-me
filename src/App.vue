<script setup>
import { ref, nextTick } from 'vue'

// 1. Estado reactivo para el idioma (Inglés por defecto)
const currentLang = ref('en')
const inputCommand = ref('')
const inputRef = ref(null)

// 2. Diccionario de traducciones (Inglés, Español, Catalán)
const i18n = {
  en: {
    welcome: 'Welcome to my interactive CV. Type "help" to see available commands.',
    notFound: 'Command not found: "{cmd}". Type "help" for assistance.',
    langChanged: 'Language successfully changed to English.',
    langError: 'Invalid language. Available options: lang [en | es | ca]',
    commands: {
      help: 'Available commands: experience, skills, contact, lang [en|es|ca], clear',
      experience: '2021-Present: Frontend Developer at Tech Corp...\n2019-2021: Junior Web Developer...',
      skills: 'JavaScript, Vue 3, Vite, CSS, HTML, Git, Linux...',
      contact: 'Email: hello@yourdomain.com | GitHub: @yourusername'
    }
  },
  es: {
    welcome: 'Bienvenido a mi CV interactivo. Escribe "help" para ver los comandos disponibles.',
    notFound: 'Comando no encontrado: "{cmd}". Escribe "help" para ayuda.',
    langChanged: 'Idioma cambiado a Español.',
    langError: 'Idioma no válido. Opciones disponibles: lang [en | es | ca]',
    commands: {
      help: 'Comandos disponibles: experience, skills, contact, lang [en|es|ca], clear',
      experience: '2021-Presente: Desarrollador Frontend en Tech Corp...\n2019-2021: Desarrollador Web Junior...',
      skills: 'JavaScript, Vue 3, Vite, CSS, HTML, Git, Linux...',
      contact: 'Email: hola@tudominio.com | GitHub: @tuusuario'
    }
  },
  ca: {
    welcome: 'Benvingut al meu currículum interactiu. Escriu "help" per veure els comandaments disponibles.',
    notFound: 'Comandament no trobat: "{cmd}". Escriu "help" per obtenir ajuda.',
    langChanged: 'Idioma canviat a Català.',
    langError: 'Idioma no vàlid. Opcions disponibles: lang [en | es | ca]',
    commands: {
      help: 'Comandaments disponibles: experience, skills, contact, lang [en|es|ca], clear',
      experience: '2021-Present: Desenvolupador Frontend a Tech Corp...\n2019-2021: Desenvolupador Web Júnior...',
      skills: 'JavaScript, Vue 3, Vite, CSS, HTML, Git, Linux...',
      contact: 'Correu: hola@elteudomini.com | GitHub: @elteuusuari'
    }
  }
}

// 3. Inicializar el historial con el mensaje de bienvenida en el idioma actual
const history = ref([
  { type: 'output', text: i18n[currentLang.value].welcome }
])

const handleCommand = async () => {
  const rawInput = inputCommand.value.trim().toLowerCase()
  if (!rawInput) return

  // Guardar lo que escribió el usuario
  history.value.push({ type: 'input', text: `invitado@tu-nombre:~$ ${rawInput}` })

  // Separar el comando del argumento (ej: "lang" y "es")
  const args = rawInput.split(' ')
  const cmd = args[0]
  const arg = args[1]

  // Lógica de procesamiento
  if (cmd === 'clear') {
    history.value = [] // clear borra la pantalla completa, no imprime texto
  } 
  else if (cmd === 'lang') {
    if (arg && ['en', 'es', 'ca'].includes(arg)) {
      currentLang.value = arg
      history.value.push({ type: 'output', text: i18n[currentLang.value].langChanged })
    } else {
      history.value.push({ type: 'output', text: i18n[currentLang.value].langError })
    }
  } 
  else if (i18n[currentLang.value].commands[cmd]) {
    history.value.push({ type: 'output', text: i18n[currentLang.value].commands[cmd] })
  } 
  else {
    // Reemplazamos {cmd} con lo que escribió el usuario para un error más dinámico
    const errorMsg = i18n[currentLang.value].notFound.replace('{cmd}', cmd)
    history.value.push({ type: 'output', text: errorMsg })
  }

  // Limpiar input y hacer scroll hacia abajo
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