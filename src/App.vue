<script setup>
import { ref, nextTick } from 'vue'

const currentLang = ref('en')
const inputCommand = ref('')
const inputRef = ref(null)

// 1. Añadimos la sección "easterEggs" a cada idioma
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
    },
    easterEggs: {
      sudo: 'Nice try. This incident will be reported to Santa Claus.',
      rm: 'Please, it\'s my CV! Don\'t delete it 😭',
      matrix: 'Wake up, Neo...',
      whoami: 'You are a curious recruiter (or just a geek). Welcome!'
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
    },
    easterEggs: {
      sudo: 'Buen intento. Este incidente será reportado.',
      rm: '¡Por favor, es mi CV! No lo borres 😭',
      matrix: 'Despierta, Neo...',
      whoami: 'Eres un recruiter curioso (o simplemente un friki). ¡Bienvenido!'
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
    },
    easterEggs: {
      sudo: 'Bon intent. Aquest incident serà reportat als Mossos.',
      rm: 'Sisplau, és el meu CV! No l\'esborris 😭',
      matrix: 'Desperta, Neo...',
      whoami: 'Ets un recruiter curiós (o simplement un friki). Benvingut!'
    }
  }
}

const history = ref([
  { type: 'output', text: i18n[currentLang.value].welcome }
])

const handleCommand = async () => {
  const rawInput = inputCommand.value.trim().toLowerCase()
  if (!rawInput) return

  history.value.push({ type: 'input', text: `invitado@arnaualio:~$ ${rawInput}` })

  const args = rawInput.split(' ')
  const cmd = args[0]
  const arg = args[1]

  // --- LÓGICA CORE ---
  if (cmd === 'clear') {
    history.value = []
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
  
  // --- EASTER EGGS 👀 ---
  else if (cmd === 'sudo') {
    history.value.push({ type: 'output', text: i18n[currentLang.value].easterEggs.sudo })
  }
  else if (rawInput === 'rm -rf /') {
    history.value.push({ type: 'output', text: i18n[currentLang.value].easterEggs.rm })
  }
  else if (cmd === 'whoami') {
    history.value.push({ type: 'output', text: i18n[currentLang.value].easterEggs.whoami })
  }
  else if (cmd === 'matrix') {
    // Esto inyecta el atributo data-theme al body para cambiar el CSS que hicimos antes
    document.body.setAttribute('data-theme', 'matrix')
    history.value.push({ type: 'output', text: i18n[currentLang.value].easterEggs.matrix })
  }
  
  // --- COMANDO NO ENCONTRADO ---
  else {
    const errorMsg = i18n[currentLang.value].notFound.replace('{cmd}', cmd)
    history.value.push({ type: 'output', text: errorMsg })
  }

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
      <span class="prompt">invitado@arnaualio:~$</span>
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