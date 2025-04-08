<script setup>
    import { inject , ref , onMounted, watch, provide } from 'vue'
    import CardList from '../components/CardList.vue'
    import axios from 'axios'
    import debounce from 'lodash.debounce'

    const cart = inject('cart')
    const items = ref([])
    const filters = ref({
            sortBy: '',
            searchQuery: '',
    })
    const toggleToCart = inject('toggleToCart')
    const onChangeSelect = (event) => {
        filters.value.sortBy = event.target.value
    }
    const onChangeQuery = debounce((event) => {
        filters.value.searchQuery = event.target.value
    },150)

    const fetchFavorite = async () => {
        try{
        const {data: favorites} = await axios.get(`https://83fbe6250193ab17.mokky.dev/favorites`)
        items.value = items.value.map((item) => {
            const favorite = favorites.find((favorite) => favorite.item_id === item.id)
            if(!favorite){
            return item
            }
            console.log(favorite.id)
            return {
            ...item,
            isFavorite: true , 
            favoriteId: favorite.id
            }
        })
        }
        catch(err){
        console.error(err)
        }
    }

    const fetchSneakers = async () => {
        try{
        const params = {
            sortBy: filters.value.sortBy,
        }
        if(filters.value.searchQuery){
            params.title = `*${filters.value.searchQuery}*`
        }
        const {data} = await axios.get(`https://83fbe6250193ab17.mokky.dev/items`,
            {params}
        )
        items.value = data.map((obj) => ({
            ...obj,
            isFavorite: false,
            isAdded: false,
            favoriteId: null
        }))
        }
        catch(err){
        console.error(err)
        }
    }

    const addToFavorite = async (item) => {
        try{
        if(!item.isFavorite){
            const obj = {
            item_id : item.id
            }
            item.isFavorite = true
            const {data} = await axios.post('https://83fbe6250193ab17.mokky.dev/favorites' , obj)
            item.favoriteId = data.id
        }
        else{
            item.isFavorite=false
            await axios.delete(`https://83fbe6250193ab17.mokky.dev/favorites/${item.favoriteId}`)
            item.favoriteId=null
        }
        }
        catch(err){
        console.error(err)
        }
  }
    
    onMounted(async () => {
        const localCart = localStorage.getItem('cart')
        cart.value = localCart ? JSON.parse(localCart) : []
        await fetchSneakers()
        await fetchFavorite()
        items.value = items.value.map((item) => ({
            ...item,
            isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
        }))
    })

    watch(filters.value , fetchSneakers)
    watch(cart, ()=> {
        items.value = items.value.map((item) => ({
            ...item,
            isAdded:false
        }))
    })

    provide('addToFavorite', addToFavorite)
    </script>

<template>
    <div>
        <div class="flex justify-between mb-8 items-center">
          <h2 class="text-3xl font-bold">Все кроссовки</h2>
          <div class="flex gap-4">
            <select @change="onChangeSelect" class="py-2 px-2 border border-gray-200 rounded-md outline-none mr-4">
              <option value="title">По названию</option>
              <option value="price">По цене(сначала дешевле)</option>
              <option value="-price">По цене(сначала дороже)</option>
            </select>
            <div class="relative">
              <img src="/search.svg" alt="" class="absolute left-4 top-3">
              <input 
                class="border border-gray-200 rounded-md py-2 pl-12 pr-4 outline-none focus:border-gray-400"
                placeholder="Поиск..."
                @input="onChangeQuery"
              />
            </div>
          </div>
        </div>
        <CardList :items="items" @add-to-favorite="addToFavorite" @toggle-to-cart="toggleToCart" />
    </div>
</template>