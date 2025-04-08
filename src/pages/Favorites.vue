<script setup>
    import axios from 'axios'
    import { onMounted, ref } from 'vue'
    import CardList from '../components/CardList.vue'

    const favorites = ref([])
    onMounted(async () => {
        try{
        const {data} = await axios.get("https://83fbe6250193ab17.mokky.dev/favorites?_relations=items")  
        favorites.value = data.map(item => item.item)
        }
        catch(err){
            console.error(err)
        }
    })
</script>

<template>
    <div>
        <h2 class="text-3xl font-bold">Мои закладки</h2>
        <CardList :items="favorites" is-favorites/>
        </div>
</template>