<script setup>
    import { inject } from 'vue'
    import CartItem from './CartItem.vue'

    const items = inject('cart')
    const toggleToCart = inject('toggleToCart')

    const onRemoveCart = (item) => {
        toggleToCart(item)
    }
</script>

<template>
    <div v-auto-animate class="flex flex-1 flex-col gap-4">
        <CartItem 
            v-for="item in items"
            :key="item.id"
            :image-url="item.imageUrl"
            :title="item.title"
            :price="item.price"
            :remove-from-cart="() => onRemoveCart(item)"
        />
    </div>
</template>