<script setup lang="ts">
const data = [
  { name: '工商银行', profit: 51.4 },
  { name: '建设银行', profit: 47.0 },
  { name: '农业银行', profit: 38.0 },
  { name: '中国银行', profit: 32.8 },
  { name: '招商银行', profit: 20.5 },
  { name: '邮储银行', profit: 13.0 },
  { name: '交通银行', profit: 12.9 },
  { name: '平安保险', profit: 12.1 },
  { name: '中国邮政', profit: 5.9 },
  { name: '中信集团', profit: 4.1 },
]

const max = Math.max(...data.map(i => i.profit))
</script>

<template>
  <div class="w-full py-2">
    <div class="space-y-3">
      <div v-for="item in data" :key="item.name" class="flex items-center text-sm group">
        <div class="w-36 font-bold opacity-80 truncate text-right pr-3 text-xs">{{ item.name }}</div>
        <div class="flex-1 h-5 bg-gray-500/10 rounded-sm relative overflow-visible">
          <div 
            class="h-full bg-gradient-to-r from-blue-600 to-blue-400 rounded-sm flex items-center transition-all duration-1000 group-hover:from-blue-500 group-hover:to-blue-300"
            :style="{ width: `${(item.profit / max) * 100}%` }"
          >
          </div>
          <span class="absolute top-0 bottom-0 flex items-center ml-2 text-xs font-mono text-black opacity-0 group-hover:opacity-100 transition-opacity left-full pl-2 whitespace-nowrap">
               ${{ item.profit }}B
            </span>
             <span class="absolute top-0 bottom-0 right-2 flex items-center text-[10px] font-mono text-black group-hover:opacity-0 transition-opacity">
               {{ item.profit }}
            </span>
        </div>
      </div>
    </div>
    <div class="mt-4 text-[10px] text-right opacity-40">
      * 2024 Est. Profit (USD Billion)
    </div>
  </div>
</template>
