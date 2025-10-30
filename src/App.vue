<template>
  <div class="min-h-screen bg-slate-950 text-slate-100 py-16">
    <div class="max-w-5xl mx-auto px-6">
      <header class="text-center space-y-3 mb-12">
        <p class="inline-flex items-center gap-2 rounded-full border border-indigo-500/40 bg-indigo-500/10 px-4 py-1 text-sm font-medium text-indigo-200">
          <span class="h-2 w-2 rounded-full bg-indigo-400"></span>
          Secure Utilities Suite
        </p>
        <h1 class="text-3xl font-semibold tracking-tight text-white sm:text-4xl">
          Encrypt &amp; Decrypt Playground
        </h1>
        <p class="mx-auto max-w-2xl text-sm text-slate-400 sm:text-base">
          Eksplorasi algoritma enkripsi kustom untuk angka tunggal maupun rentang nilai dengan antarmuka modern berbasis Tailwind CSS.
        </p>
      </header>

      <div class="rounded-3xl border border-slate-800 bg-slate-900/60 p-6 shadow-2xl shadow-slate-950/50 backdrop-blur md:p-10">
        <div class="mb-8 flex justify-center gap-2 rounded-full border border-slate-800 bg-slate-900/60 p-1 text-sm font-medium">
          <button
            type="button"
            @click="setMode('single')"
            :class="[
              'flex-1 rounded-full px-4 py-2 transition',
              mode === 'single'
                ? 'bg-indigo-500 text-white shadow-lg shadow-indigo-500/40'
                : 'text-slate-400 hover:text-slate-200'
            ]"
          >
            Nilai Tunggal
          </button>
          <button
            type="button"
            @click="setMode('range')"
            :class="[
              'flex-1 rounded-full px-4 py-2 transition',
              mode === 'range'
                ? 'bg-indigo-500 text-white shadow-lg shadow-indigo-500/40'
                : 'text-slate-400 hover:text-slate-200'
            ]"
          >
            Rentang Angka
          </button>
        </div>

        <div v-if="mode === 'single'" class="grid gap-6 md:grid-cols-[1fr_auto] md:items-end">
          <div class="space-y-2">
            <label for="single-value" class="text-sm font-medium text-slate-300">Masukkan angka atau teks</label>
            <input
              id="single-value"
              v-model="input"
              type="text"
              placeholder="Contoh: 1234 atau SnTT"
              class="w-full rounded-2xl border border-slate-700 bg-slate-900 px-4 py-3 text-base text-slate-100 shadow-inner shadow-black/20 outline-none transition focus:border-indigo-400 focus:ring-2 focus:ring-indigo-400/60"
            />
          </div>
          <div class="flex gap-3 md:flex-col">
            <button
              type="button"
              @click="handleEncrypt"
              class="flex-1 rounded-2xl bg-indigo-500 px-6 py-3 font-semibold text-white shadow-lg shadow-indigo-500/40 transition hover:bg-indigo-400"
            >
              Encrypt
            </button>
            <button
              type="button"
              @click="handleDecrypt"
              class="flex-1 rounded-2xl bg-emerald-500 px-6 py-3 font-semibold text-white shadow-lg shadow-emerald-500/40 transition hover:bg-emerald-400"
            >
              Decrypt
            </button>
          </div>
        </div>

        <div v-else class="space-y-8">
          <div class="grid gap-6 md:grid-cols-2">
            <div class="space-y-2">
              <label for="range-start" class="text-sm font-medium text-slate-300">Angka awal</label>
              <input
                id="range-start"
                v-model.number="rangeStart"
                type="number"
                class="w-full rounded-2xl border border-slate-700 bg-slate-900 px-4 py-3 text-base text-slate-100 shadow-inner shadow-black/20 outline-none transition focus:border-indigo-400 focus:ring-2 focus:ring-indigo-400/60"
              />
            </div>
            <div class="space-y-2">
              <label for="range-end" class="text-sm font-medium text-slate-300">Angka akhir</label>
              <input
                id="range-end"
                v-model.number="rangeEnd"
                type="number"
                class="w-full rounded-2xl border border-slate-700 bg-slate-900 px-4 py-3 text-base text-slate-100 shadow-inner shadow-black/20 outline-none transition focus:border-indigo-400 focus:ring-2 focus:ring-indigo-400/60"
              />
              <p class="text-xs text-slate-500">Rentang maksimal {{ maxRangeSpan }} angka.</p>
            </div>
          </div>

          <div class="space-y-3">
            <div class="flex items-center justify-between text-sm text-slate-400">
              <span>Sesuaikan rentang</span>
              <span class="font-medium text-slate-200">
                {{ displayRangeStart }} &rarr; {{ displayRangeEnd }}
                <span class="text-slate-400">({{ plannedRangeCount }} angka)</span>
              </span>
            </div>
            <input
              type="range"
              :min="rangeStart"
              :max="rangeStart + maxRangeSpan"
              v-model.number="rangeEnd"
              class="range accent-indigo-500"
            />
          </div>

          <div class="flex flex-col gap-3 sm:flex-row">
            <button
              type="button"
              @click="handleEncryptRange"
              class="flex-1 rounded-2xl bg-indigo-500 px-6 py-3 font-semibold text-white shadow-lg shadow-indigo-500/40 transition hover:bg-indigo-400"
            >
              Encrypt Rentang
            </button>
            <button
              type="button"
              @click="resetRangeResults"
              class="flex-1 rounded-2xl border border-slate-700 px-6 py-3 font-semibold text-slate-200 transition hover:border-slate-500 hover:text-white"
            >
              Bersihkan Hasil
            </button>
          </div>
        </div>
      </div>

      <transition name="fade">
        <div v-if="mode === 'single' && result !== ''" class="mt-10 rounded-3xl border border-slate-800 bg-slate-900/70 p-6 shadow-xl">
          <h2 class="text-lg font-semibold text-white">Hasil</h2>
          <p class="mt-3 break-all rounded-2xl bg-slate-950/60 px-4 py-3 text-sm text-indigo-100">
            {{ result }}
          </p>
        </div>
      </transition>

      <transition name="fade">
        <div v-if="mode === 'range' && rangeResults.length" class="mt-10 space-y-4">
          <div class="flex flex-wrap items-center justify-between gap-2">
            <h2 class="text-lg font-semibold text-white">Daftar Enkripsi</h2>
            <p class="text-sm text-slate-400">Total {{ rangeResults.length }} angka terenkripsi.</p>
          </div>
          <div class="overflow-hidden rounded-3xl border border-slate-800 bg-slate-900/70 shadow-xl">
            <div class="max-h-[480px] overflow-y-auto">
              <table class="min-w-full divide-y divide-slate-800 text-left text-sm">
                <thead class="bg-slate-900/80 text-xs uppercase tracking-wide text-slate-400">
                  <tr>
                    <th scope="col" class="px-4 py-3">Angka</th>
                    <th scope="col" class="px-4 py-3">Hasil Encrypt</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-slate-800">
                  <tr v-for="item in rangeResults" :key="item.number" class="hover:bg-slate-900/80">
                    <td class="px-4 py-3 font-medium text-slate-100">{{ item.number }}</td>
                    <td class="px-4 py-3 text-indigo-100">{{ item.encrypted }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

const input = ref('')
const result = ref('')
const mode = ref('single')
const maxRangeSpan = 500
const rangeStart = ref(3200)
const rangeEnd = ref(3200)
const rangeResults = ref([])

const displayRangeStart = computed(() => rangeStart.value.toLocaleString('id-ID'))
const displayRangeEnd = computed(() => rangeEnd.value.toLocaleString('id-ID'))
const plannedRangeCount = computed(() => Math.max(1, rangeEnd.value - rangeStart.value + 1))

watch(rangeStart, (value, oldValue) => {
  if (!Number.isFinite(value)) {
    rangeStart.value = oldValue ?? 0
    return
  }

  const normalizedStart = Math.round(value)
  if (normalizedStart !== value) {
    rangeStart.value = normalizedStart
    return
  }

  const minEnd = normalizedStart
  const maxEnd = normalizedStart + maxRangeSpan

  if (rangeEnd.value < minEnd) {
    rangeEnd.value = minEnd
  }

  if (rangeEnd.value > maxEnd) {
    rangeEnd.value = maxEnd
  }
})

watch(rangeEnd, (value, oldValue) => {
  if (!Number.isFinite(value)) {
    rangeEnd.value = oldValue ?? rangeStart.value
    return
  }

  const normalizedEnd = Math.round(value)
  if (normalizedEnd !== value) {
    rangeEnd.value = normalizedEnd
    return
  }

  const minEnd = rangeStart.value
  const maxEnd = rangeStart.value + maxRangeSpan

  if (normalizedEnd < minEnd) {
    rangeEnd.value = minEnd
  }

  if (normalizedEnd > maxEnd) {
    rangeEnd.value = maxEnd
  }
})

function setMode(targetMode) {
  mode.value = targetMode
  if (targetMode === 'single') {
    rangeResults.value = []
  } else {
    result.value = ''
  }
}

function encrypt(arg) {
  const baseValue = parseInt(arg)
  const jump = ((baseValue * 1996) + 8) / 2
  const baseStrToChr = String(jump).split('')
  let encryptValue = ''
  const codeOne = ['w4Hy', 'Fg4c', 'as60', 'D8cr', 'WnT6', 'kls0', 'SnTT', 'c008', 'LSqP', 'T6Ra']
  const codeBigOne = ['x', 'j', 'V', 'p', 'K', 'd', 'f', 'Y', '5', 't']
  const codeSave = ['M', 'c', 'B', 'n', 'N', 'm', 'Q', 'u', 'X', 'q']

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
  const codeOne = ['w4Hy', 'Fg4c', 'as60', 'D8cr', 'WnT6', 'kls0', 'SnTT', 'c008', 'LSqP', 'T6Ra']
  const codeBigOne = ['x', 'j', 'V', 'p', 'K', 'd', 'f', 'Y', '5', 't']
  const codeSave = ['M', 'c', 'B', 'n', 'N', 'm', 'Q', 'u', 'X', 'q']

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
  rangeResults.value = []
}

function handleDecrypt() {
  if (input.value.trim() === '') return
  result.value = decrypt(input.value)
  rangeResults.value = []
}

function handleEncryptRange() {
  const start = Math.round(rangeStart.value)
  const end = Math.round(rangeEnd.value)
  const from = Math.min(start, end)
  const to = Math.max(start, end)

  if (to - from > maxRangeSpan) {
    return
  }

  const values = []
  for (let current = from; current <= to; current += 1) {
    values.push({
      number: current,
      encrypted: encrypt(current)
    })
  }

  rangeResults.value = values
  result.value = ''
}

function resetRangeResults() {
  rangeResults.value = []
}
</script>
