<template>
    <div class="flex flex-col flex-1">
        <div class="flex flex-col py-2 items-center">
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-2"></AnimatedPlaceholder>
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-12"></AnimatedPlaceholder>
            <AnimatedPlaceholder class="max-w-[300px] h-[100px] w-full mb-12"></AnimatedPlaceholder>
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-8"></AnimatedPlaceholder>
            <AnimatedPlaceholder class="max-w-[300px] h-[75px] w-full"></AnimatedPlaceholder>
        </div>
    </div>
</template>

<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';
</script>