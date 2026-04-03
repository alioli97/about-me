<script setup>
import { ref, nextTick } from 'vue'

const currentLang = ref('en')
const inputCommand = ref('')
const inputRef = ref(null)

// Lista de temas disponibles
const availableThemes = ['dark', 'light', 'matrix', 'dracula', 'ubuntu', 'retro']

// 1. Actualizamos el diccionario con los mensajes del tema
const i18n = {
  en: {
    welcome: 'Welcome to Arnau Alió Parull\'s interactive CV. Type "help" to see available commands.',
    notFound: 'Command not found: "{cmd}". Type "help" for assistance.',
    langChanged: 'Language successfully changed to English.',
    langError: 'Invalid language. Available options: lang [en | es | ca]',
    themeChanged: 'Theme changed to: {theme}',
    themeError: 'Invalid theme. Available options: theme [dark | light | matrix | dracula | ubuntu | retro]',
    commands: {
      help: 'Available commands: about, experience, education, skills, contact, lang [en|es|ca], theme [name], clear',
      about: 'Hi! I am Arnau Alió Parull.\nTelecommunications Systems Engineer specialized in Electronics.',
      experience: '💼 EXPERIENCE\n\n[Jan 2022 - Present] Embedded Software Engineer - Automotive Systems @ IDNEO\n[Mar 2021 - Jan 2022] Embedded System Software Engineer Intern @ IDNEO\n[Feb 2020 - Oct 2020] Quality Assurance Intern @ XpertAl\n[May 2018 - Aug 2019] Teaching Assistant @ UPC (CTT) - Data management, web updates & SAP\n[May 2017 - Jul 2019] Percussion Workshop Leader @ La Galeta percussió',
      education: '🎓 EDUCATION\n\n[2015 - 2020] Degree in Telecommunications Technologies and Services Engineering, Electronic Systems @ UPC-ETSETB\n[Dec 2025] Degree in Economics @ UOC\n\nCertifications:\n- Linux kernel driver development\n- Intro to AI Development',
      skills: '🛠️ SKILLS\n\nCore: C Programming, Embedded Software, Linux kernel driver development, Python\nTools: JIRA, SAP, Genweb, Plone\nLanguages: Catalan (Native), Spanish (Native), English (Professional Working)',
      contact: '📫 CONTACT\n\nEmail: arnau.alio.fcb@gmail.com\nLinkedIn: www.linkedin.com/in/arnaualio'
    },
    easterEggs: {
      sudo: 'Nice try. This incident will be reported.',
      rm: 'Please, it\'s my CV! Don\'t delete it 😭',
      matrix: 'Wake up, Arnau...',
      whoami: 'You are a curious recruiter (or just a geek). Welcome!'
    }
  },
  es: {
    welcome: 'Bienvenido al CV interactivo de Arnau Alió Parull. Escribe "help" para ver los comandos disponibles.',
    notFound: 'Comando no encontrado: "{cmd}". Escribe "help" para ayuda.',
    langChanged: 'Idioma cambiado a Español.',
    langError: 'Idioma no válido. Opciones disponibles: lang [en | es | ca]',
    themeChanged: 'Tema cambiado a: {theme}',
    themeError: 'Tema no válido. Opciones: theme [dark | light | matrix | dracula | ubuntu | retro]',
    commands: {
      help: 'Comandos disponibles: about, experience, education, skills, contact, lang [en|es|ca], theme [nombre], clear',
      about: '¡Hola! Soy Arnau Alió Parull.\nIngeniero de Sistemas de Telecomunicación especializado en Electrónica.',
      experience: '💼 EXPERIENCIA\n\n[Ene 2022 - Presente] Embedded Software Engineer - Automotive Systems en IDNEO\n[Mar 2021 - Ene 2022] Embedded System Software Engineer Intern en IDNEO\n[Feb 2020 - Oct 2020] Quality Assurance Intern en XpertAl\n[May 2018 - Ago 2019] Becario de enseñanza en CTT de la UPC - Gestión de datos, web y SAP\n[May 2017 - Jul 2019] Tallerista de Percusión en La Galeta percussió',
      education: '🎓 EDUCACIÓN\n\n[2015 - 2020] Grau d\'Enginyeria de Tecnologies i Serveis de Telecomunicació, Electronic Systems en UPC-ETSETB\n[Dic 2025] Grau en Economia en UOC\n\nCertificaciones:\n- Linux kernel driver development\n- Curso de Iniciación al Desarrollo con IA',
      skills: '🛠️ HABILIDADES\n\nCore: Programación en C, Software embebido, Desarrollo de drivers del kernel de Linux, Python\nHerramientas: JIRA, SAP, Genweb, Plone\nIdiomas: Catalán (Nativo), Español (Nativo), Inglés (Profesional)',
      contact: '📫 CONTACTO\n\nEmail: arnau.alio.fcb@gmail.com\nLinkedIn: www.linkedin.com/in/arnaualio'
    },
    easterEggs: {
      sudo: 'Buen intento. Este incidente será reportado.',
      rm: '¡Por favor, es mi CV! No lo borres 😭',
      matrix: 'Despierta, Arnau...',
      whoami: 'Eres un recruiter curioso (o simplemente un friki). ¡Bienvenido!'
    }
  },
  ca: {
    welcome: 'Benvingut al CV interactiu d\'Arnau Alió Parull. Escriu "help" per veure els comandaments disponibles.',
    notFound: 'Comandament no trobat: "{cmd}". Escriu "help" per obtenir ajuda.',
    langChanged: 'Idioma canviat a Català.',
    langError: 'Idioma no vàlid. Opcions disponibles: lang [en | es | ca]',
    themeChanged: 'Tema canviat a: {theme}',
    themeError: 'Tema no vàlid. Opcions: theme [dark | light | matrix | dracula | ubuntu | retro]',
    commands: {
      help: 'Comandaments disponibles: about, experience, education, skills, contact, lang [en|es|ca], theme [nom], clear',
      about: 'Hola! Soc l\'Arnau Alió Parull.\nEnginyer de Sistemes de Telecomunicació especialitzat en Electrònica.',
      experience: '💼 EXPERIÈNCIA\n\n[Gen 2022 - Present] Embedded Software Engineer - Automotive Systems a IDNEO\n[Mar 2021 - Gen 2022] Embedded System Software Engineer Intern a IDNEO\n[Feb 2020 - Oct 2020] Quality Assurance Intern a XpertAl\n[Mai 2018 - Ago 2019] Becari d\'ensenyament al CTT de la UPC - Gestió de dades, web i SAP\n[Mai 2017 - Jul 2019] Tallerista de Percussió a La Galeta percussió',
      education: '🎓 EDUCACIÓ\n\n[2015 - 2020] Grau d\'Enginyeria de Tecnologies i Serveis de Telecomunicació, Electronic Systems a UPC-ETSETB\n[Des 2025] Grau en Economia a UOC\n\nCertificacions:\n- Linux kernel driver development\n- Curs d\'Iniciació al Desenvolupament amb IA',
      skills: '🛠️ HABILITATS\n\nCore: Programació en C, Programari incrustat (Embedded), Linux kernel driver development, Python\nEines: JIRA, SAP, Genweb, Plone\nIdiomes: Català (Nadiu), Espanyol (Nadiu), Anglès (Professional)',
      contact: '📫 CONTACTE\n\nCorreu: arnau.alio.fcb@gmail.com\nLinkedIn: www.linkedin.com/in/arnaualio'
    },
    easterEggs: {
      sudo: 'Bon intent. Aquest incident serà reportat.',
      rm: 'Sisplau, és el meu CV! No l\'esborris 😭',
      matrix: 'Desperta, Arnau...',
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
  // 🟢 AQUÍ ESTÁ EL NUEVO COMANDO THEME
  else if (cmd === 'theme') {
    if (arg && availableThemes.includes(arg)) {
      document.body.setAttribute('data-theme', arg)
      const successMsg = i18n[currentLang.value].themeChanged.replace('{theme}', arg)
      history.value.push({ type: 'output', text: successMsg })
    } else {
      history.value.push({ type: 'output', text: i18n[currentLang.value].themeError })
    }
  }
  else if (i18n[currentLang.value].commands[cmd]) {
    history.value.push({ type: 'output', text: i18n[currentLang.value].commands[cmd] })
  } 
  
  // --- EASTER EGGS ---
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