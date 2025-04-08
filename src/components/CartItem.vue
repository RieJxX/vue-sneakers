<script setup>
    defineProps({
        imageUrl: String,
        title: String,
        price:String,
        removeFromCart:Function
    })
</script>

<template>
    <div class="border border-slate-200 p-4 rounded-xl flex items-center gap-4">
        <img class="w-16 h-16" :src="imageUrl" alt="Sneaker">
        <div class="flex flex-col w-full">
            <p>{{title}}</p>
            <div class="flex justify-between mt-2">
                <b>{{price}} руб.</b>
                <img @click="removeFromCart" class="opacity-40 hover:opacity-100 transition cursor-pointer" src="/close.svg">
            </div>
        </div>
    </div>
</template>