<template>
  <div class="min-h-screen bg-gradient-to-br from-gray-900 via-gray-800 to-gray-900 text-gray-100 p-4">
    <div class="max-w-4xl mx-auto">
      <header class="text-center mb-8">
        <h1 class="text-5xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-400 mb-2">Discord Timestamp Generator</h1>
        <p class="text-gray-400">Generate Discord-compatible timestamps easily</p>
      </header>

      <div class="grid md:grid-cols-2 gap-6">
        <!-- Natural Language Input Section -->
        <div class="bg-gray-800/50 backdrop-blur-sm rounded-lg p-6 shadow-lg border border-purple-500/20">
          <h2 class="text-2xl font-semibold text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-400 mb-4">AI Generation</h2>
          <div class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-300 mb-2">Natural Language Input</label>
              <input
                v-model="naturalInput"
                @keyup.enter="generateTimestamp"
                type="text"
                placeholder="e.g., next Friday at 3 PM"
                class="w-full px-4 py-2 bg-gray-700/50 border border-purple-500/20 rounded-md focus:ring-2 focus:ring-purple-500 focus:border-transparent"
              />
              <div class="mt-2 text-sm text-gray-400">
                Try phrases like:
                <div class="grid grid-cols-2 gap-2 mt-1">
                  <button 
                    v-for="example in examples" 
                    :key="example"
                    @click="useExample(example)"
                    class="text-left text-xs px-2 py-1 rounded bg-gray-700/30 hover:bg-gray-700/50 transition-colors"
                  >
                    {{ example }}
                  </button>
                </div>
              </div>
            </div>
            <button
              @click="generateTimestamp"
              class="w-full bg-gradient-to-r from-purple-500 to-pink-500 hover:from-purple-600 hover:to-pink-600 text-white font-medium py-2 px-4 rounded-md transition-all transform hover:scale-[1.02]"
            >
              Generate with AI
            </button>
          </div>
        </div>

        <!-- Manual Input Section -->
        <div class="bg-gray-800/50 backdrop-blur-sm rounded-lg p-6 shadow-lg border border-pink-500/20">
          <h2 class="text-2xl font-semibold text-transparent bg-clip-text bg-gradient-to-r from-pink-400 to-purple-400 mb-4">Manual Input</h2>
          <div class="space-y-4">
            <div class="grid grid-cols-2 gap-4">
              <div>
                <label class="block text-sm font-medium text-gray-300 mb-2">Date</label>
                <input
                  v-model="manualDate"
                  type="date"
                  class="w-full px-4 py-2 bg-gray-700/50 border border-pink-500/20 rounded-md focus:ring-2 focus:ring-pink-500 focus:border-transparent"
                />
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-300 mb-2">Time</label>
                <input
                  v-model="manualTime"
                  type="time"
                  class="w-full px-4 py-2 bg-gray-700/50 border border-pink-500/20 rounded-md focus:ring-2 focus:ring-pink-500 focus:border-transparent"
                />
              </div>
            </div>
            <button
              @click="generateManualTimestamp"
              class="w-full bg-gradient-to-r from-pink-500 to-purple-500 hover:from-pink-600 hover:to-purple-600 text-white font-medium py-2 px-4 rounded-md transition-all transform hover:scale-[1.02]"
            >
              Generate Manually
            </button>
          </div>
        </div>
      </div>

      <!-- Quick Actions -->
      <div class="mt-6">
        <button
          @click="useCurrentTime"
          class="w-full bg-gray-700/50 hover:bg-gray-600/50 text-white font-medium py-2 px-4 rounded-md transition-all transform hover:scale-[1.02] border border-gray-600/20"
        >
          Use Current Time
        </button>
      </div>

      <!-- Results Section -->
      <div v-if="timestamp" class="mt-6">
        <div class="bg-gray-800/50 backdrop-blur-sm rounded-lg p-6 shadow-lg border border-gray-600/20">
          <h2 class="text-2xl font-semibold text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-400 mb-4">Generated Timestamps</h2>
          <div class="grid gap-4">
            <div v-for="(format, index) in timestampFormats" :key="index" 
                 class="bg-gray-700/50 p-4 rounded-lg border border-gray-600/20 transition-all hover:border-purple-500/20">
              <div class="flex justify-between items-center">
                <div>
                  <span class="text-sm text-gray-400">{{ format.description }}</span>
                  <div class="font-mono text-sm mt-1 text-purple-300">{{ format.code }}</div>
                </div>
                <button
                  @click="copyToClipboard(format.code)"
                  class="ml-4 px-4 py-2 text-sm bg-gradient-to-r from-purple-500/20 to-pink-500/20 hover:from-purple-500/30 hover:to-pink-500/30 rounded-md transition-all transform hover:scale-[1.02] border border-purple-500/20"
                >
                  Copy
                </button>
              </div>
              <div class="mt-2 text-gray-300">Preview: {{ format.preview }}</div>
            </div>
          </div>
        </div>
      </div>

      <footer class="mt-8 text-center text-gray-400 text-sm">
        <p>Built with Vue.js and Compromise NLP</p>
        <div class="flex items-center justify-center gap-2 mt-2">
          <p class="text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-400">Made by pandadevv</p>
          <a href="https://github.com/jackh54/Discord-Timestamp-Gen" 
             target="_blank" 
             rel="noopener noreferrer"
             class="text-gray-400 hover:text-white transition-all duration-300 transform hover:scale-110">
            <i class="fab fa-github text-xl hover:drop-shadow-[0_0_8px_rgba(255,255,255,0.5)]"></i>
          </a>
        </div>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import * as chrono from 'chrono-node'
import { format as formatDate, fromUnixTime, getUnixTime, parse, addMinutes, addHours, addDays, addWeeks } from 'date-fns'
import { useToast } from 'vue-toast-notification'
import 'vue-toast-notification/dist/theme-sugar.css'

const toast = useToast()
const naturalInput = ref('')
const manualDate = ref('')
const manualTime = ref('')
const timestamp = ref(null)

const examples = [
  'next Friday at 3 PM',
  'tomorrow at noon',
  'in 2 hours',
  'next week',
  'this weekend',
  'next month',
  'in 30 minutes',
  'tonight at 8'
]

function useExample(example) {
  naturalInput.value = example
  generateTimestamp()
}

const timestampFormats = computed(() => {
  if (!timestamp.value) return []
  
  const formats = [
    { style: 't', description: 'Short Time', example: '16:20' },
    { style: 'T', description: 'Long Time', example: '16:20:30' },
    { style: 'd', description: 'Short Date', example: '20/04/2023' },
    { style: 'D', description: 'Long Date', example: '20 April 2023' },
    { style: 'f', description: 'Short Date/Time', example: '20 April 2023 16:20' },
    { style: 'F', description: 'Long Date/Time', example: 'Thursday, 20 April 2023 16:20' },
    { style: 'R', description: 'Relative Time', example: '2 hours ago' }
  ]

  return formats.map(f => ({
    description: f.description,
    code: `<t:${timestamp.value}:${f.style}>`,
    preview: formatDiscordTimestamp(timestamp.value, f.style)
  }))
})

function formatDiscordTimestamp(timestamp, style) {
  const date = fromUnixTime(timestamp)
  
  switch (style) {
    case 't':
      return formatDate(date, 'HH:mm')
    case 'T':
      return formatDate(date, 'HH:mm:ss')
    case 'd':
      return formatDate(date, 'dd/MM/yyyy')
    case 'D':
      return formatDate(date, 'dd MMMM yyyy')
    case 'f':
      return formatDate(date, 'dd MMMM yyyy HH:mm')
    case 'F':
      return formatDate(date, 'EEEE, dd MMMM yyyy HH:mm')
    case 'R':
      const now = new Date()
      const diffInSeconds = Math.floor((date - now) / 1000)
      if (diffInSeconds < 0) {
        const absSeconds = Math.abs(diffInSeconds)
        if (absSeconds < 60) return 'just now'
        if (absSeconds < 3600) return `${Math.floor(absSeconds / 60)} minutes ago`
        if (absSeconds < 86400) return `${Math.floor(absSeconds / 3600)} hours ago`
        return `${Math.floor(absSeconds / 86400)} days ago`
      }
      if (diffInSeconds < 60) return 'in a few seconds'
      if (diffInSeconds < 3600) return `in ${Math.floor(diffInSeconds / 60)} minutes`
      if (diffInSeconds < 86400) return `in ${Math.floor(diffInSeconds / 3600)} hours`
      return `in ${Math.floor(diffInSeconds / 86400)} days`
    default:
      return 'Invalid format'
  }
}

function generateTimestamp() {
  try {
    if (!naturalInput.value) {
      toast.error('Please enter a date/time')
      return
    }

    const input = naturalInput.value.toLowerCase().trim()
    const now = new Date()
    
    // Handle special cases first
    if (input === 'now') {
      timestamp.value = getUnixTime(now)
      toast.success('Timestamp generated successfully!')
      return
    }

    // Try parsing with chrono
    const results = chrono.parse(input, now, { forwardDate: true })
    
    if (results.length > 0) {
      const parsedDate = results[0].start.date()
      if (!isNaN(parsedDate.getTime())) {
        // For relative times like "in X minutes/hours", ensure we're using the current time
        if (input.startsWith('in ') || input.includes('from now')) {
          parsedDate.setSeconds(0) // Reset seconds for cleaner timestamps
        }
        // For dates without times, set to noon by default
        else if (results[0].start.isCertain('day') && !results[0].start.isCertain('hour')) {
          parsedDate.setHours(12, 0, 0, 0)
        }
        
        timestamp.value = getUnixTime(parsedDate)
        toast.success('Timestamp generated successfully!')
        return
      }
    }

    // Try direct date parsing as fallback
    const directParsed = new Date(input)
    if (!isNaN(directParsed.getTime())) {
      timestamp.value = getUnixTime(directParsed)
      toast.success('Timestamp generated successfully!')
      return
    }

    toast.error('Could not parse the date/time. Try clicking one of the example formats below the input.')
  } catch (error) {
    console.error('Date parsing error:', error)
    toast.error('Error parsing date/time. Please try the manual input instead.')
  }
}

function generateManualTimestamp() {
  try {
    if (!manualDate.value || !manualTime.value) {
      toast.error('Please enter both date and time')
      return
    }

    const dateTimeStr = `${manualDate.value} ${manualTime.value}`
    const parsedDate = new Date(`${dateTimeStr}`)
    
    if (isNaN(parsedDate.getTime())) {
      toast.error('Invalid date/time combination')
      return
    }

    timestamp.value = getUnixTime(parsedDate)
    toast.success('Timestamp generated successfully!')
  } catch (error) {
    toast.error('Error generating timestamp')
    console.error(error)
  }
}

function useCurrentTime() {
  timestamp.value = getUnixTime(new Date())
  naturalInput.value = 'now'
  manualDate.value = formatDate(new Date(), 'yyyy-MM-dd')
  manualTime.value = formatDate(new Date(), 'HH:mm')
  toast.success('Using current time')
}

async function copyToClipboard(text) {
  try {
    await navigator.clipboard.writeText(text)
    toast.success('Copied to clipboard!')
  } catch (error) {
    toast.error('Failed to copy to clipboard')
    console.error(error)
  }
}
</script>

<style>
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

input[type="date"],
input[type="time"] {
  color-scheme: dark;
}
</style> 