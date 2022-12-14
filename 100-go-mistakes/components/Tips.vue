<!--
Simple Arrow
<arrow x1="10" y1="20" x2="100" y2="200" color="green" width="3" />
<arrow v-bind="{ x1:10, y1:10, x2:200, y2:200 }"/>
-->

<script setup lang="ts">
import { customAlphabet } from 'nanoid'
defineProps<{
  x1: number | string
  x2: number | string
  y: number | string
  width?: number | string
  color?: string
  fs: string
  text: string
}>()
const nanoid = customAlphabet('abcedfghijklmn', 10)
const id = nanoid()
</script>

<template>
  <svg
    class="absolute left-0 top-0 pointer-events-none"
    :width="Math.max(+x1, +x2) + 200"
    :height="Math.max(+y) + 50"
  >
    <defs>
      <marker
        :id="id"
        markerUnits="strokeWidth"
        :markerWidth="10"
        :markerHeight="7"
        refX="9"
        refY="3.5"
        orient="auto"
      >
        <polygon points="0 0, 10 3.5, 0 7" :fill="color || 'currentColor'" />
      </marker>
    </defs>
    <line
      :x1="+x1"
      :y1="+y"
      :x2="+x2"
      :y2="+y"
      :stroke="color || 'currentColor'"
      :stroke-width="width || 2"
      :marker-end="`url(#${id})`"
    />
    <text :x="+x1+5" :y="+y+5" fill="#000000" font-size="16px" text-anchor="start" style="white-space: pre;" direction="ltr" >{{text}}</text>
  </svg>
</template>