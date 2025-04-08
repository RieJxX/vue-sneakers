<script setup>
    import Card from './Card.vue'
    const props = defineProps({
        items: Array,
        isFavorites: Boolean
    })
    const emit = defineEmits(['addToFavorite' , 'toggleToCart'])
</script>

<template>
    <div class="grid grid-cols-4 gap-5" v-auto-animate>
        <Card 
            :key="item.id"
            :id="item.id"
            v-for="item in items"
            :image-url="item.imageUrl" 
            :title="item.title" 
            :price="item.price" 
            :is-favorite="item.isFavorite"
            :is-added="item.isAdded"
            :on-click-favorite="isFavorites ? null : () => emit('addToFavorite' , item)"
            :on-click-cart="isFavorites ? null : () => emit('toggleToCart' , item)"
        />
    </div>
</template>