<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-gray-100 p-6">
    <h1 class="text-2xl font-bold mb-4">Encrypt / Decrypt Demo</h1>

    <input
      v-model="input"
      type="text"
      placeholder="Enter text or number"
      class="border rounded px-3 py-2 w-80 mb-4"
    />

    <div class="flex gap-3 mb-6">
      <button @click="handleEncrypt" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">Encrypt</button>
      <button @click="handleDecrypt" class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700">Decrypt</button>
    </div>

    <div v-if="result !== ''" class="bg-white shadow rounded p-4 w-80 text-center">
      <p class="font-semibold mb-1">Result:</p>
      <p class="text-lg break-all">{{ result }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const input = ref('')
const result = ref('')

function encrypt(arg) {
  const baseValue = parseInt(arg)
  const jump = ((baseValue * 1996) + 8) / 2
  const baseStrToChr = String(jump).split('')
  let encryptValue = ''
  const codeOne = ["w4Hy", "Fg4c", "as60", "D8cr", "WnT6", "kls0", "SnTT", "c008", "LSqP", "T6Ra"]
  const codeBigOne = ["x", "j", "V", "p", "K", "d", "f", "Y", "5", "t"]
  const codeSave = ["M", "c", "B", "n", "N", "m", "Q", "u", "X", "q"]

  if (jump < 10) {
    encryptValue = codeOne[jump]
  } else {
    let save = 0
    for (let i = 0; i < baseStrToChr.length; i++) {
      for (let j = 0; j < 10; j++) {
        if (parseInt(baseStrToChr[i]) === j) {
          if (encryptValue === '') {
            encryptValue += codeBigOne[j]
          } else {
            const lastChar = encryptValue.slice(-1)
            if (lastChar === codeBigOne[j]) {
              encryptValue += codeSave[save++]
            } else {
              encryptValue += codeBigOne[j]
            }
          }
          break
        }
      }
    }
  }
  return encryptValue
}

function decrypt(arg) {
  const baseValue = String(arg)
  let encode = 0
  let tmpencode = ''
  let injection = 0
  const codeOne = ["w4Hy", "Fg4c", "as60", "D8cr", "WnT6", "kls0", "SnTT", "c008", "LSqP", "T6Ra"]
  const codeBigOne = ["x", "j", "V", "p", "K", "d", "f", "Y", "5", "t"]
  const codeSave = ["M", "c", "B", "n", "N", "m", "Q", "u", "X", "q"]

  for (let i = 0; i < 10; i++) {
    if (baseValue === codeOne[i]) {
      encode = i
      break
    }
  }

  if (encode === 0) {
    const baseToChr = baseValue.split('')
    for (let i = 0; i < baseToChr.length; i++) {
      let confirm = 0
      for (let j = 0; j < 10; j++) {
        if (baseToChr[i] === codeBigOne[j]) {
          tmpencode += j
          confirm = 1
          break
        }
      }
      if (confirm === 0) {
        let validation = 0
        for (let j = 0; j < 10; j++) {
          if (baseToChr[i] === codeSave[j]) {
            const tmpp = tmpencode.split('')
            tmpencode += tmpp[tmpp.length - 1]
            validation = 1
            break
          }
        }
        if (validation === 0) {
          tmpencode = '0'
          injection = 1
          break
        }
      }
    }

    if (injection === 0) {
      const tmp = parseInt(tmpencode)
      encode = ((tmp * 2) - 8) / 1996
    } else {
      encode = 0
    }
  }

  return parseInt(encode)
}

function handleEncrypt() {
  if (input.value.trim() === '') return
  result.value = encrypt(input.value)
}

function handleDecrypt() {
  if (input.value.trim() === '') return
  result.value = decrypt(input.value)
}
</script>

<style>
body {
  margin: 0;
  font-family: system-ui, sans-serif;
}
</style>
