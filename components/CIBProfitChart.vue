<script setup lang="ts">
const data = [
  { year: '2020', profit: 666.26 },
  { year: '2021', profit: 826.80 },
  { year: '2022', profit: 913.77 },
  { year: '2023', profit: 771.16 },
  { year: '2024', profit: 772.05 },
]

const max = Math.max(...data.map(i => i.profit))
</script>

<template>
  <div class="w-full py-2 pr-12">
    <div class="space-y-4">
      <div v-for="item in data" :key="item.year" class="flex items-center text-sm group">
        <div class="w-16 font-bold opacity-80 text-right pr-3 text-sm">{{ item.year }}</div>
        <div class="flex-1 h-8 bg-gray-500/10 rounded-sm relative overflow-visible">
          <div 
            class="h-full bg-gradient-to-r from-blue-600 to-blue-400 rounded-sm flex items-center transition-all duration-1000 group-hover:from-blue-500 group-hover:to-blue-300"
            :style="{ width: `${(item.profit / max) * 100}%` }"
          >
          </div>
          <span class="absolute top-0 bottom-0 flex items-center ml-2 text-sm font-mono text-black opacity-0 group-hover:opacity-100 transition-opacity left-full pl-2 whitespace-nowrap">
               {{ item.profit }} 亿元
            </span>
             <span class="absolute top-0 bottom-0 right-2 flex items-center text-xs font-mono text-black group-hover:opacity-0 transition-opacity">
               {{ item.profit }}
            </span>
        </div>
      </div>
    </div>
    <div class="mt-6 text-[10px] text-right opacity-40">
      * 单位：人民币（亿元）
    </div>
  </div>
</template>
