<script setup>
  import Header from './components/Header.vue'
  import Drawer from './components/Drawer.vue'
  import Home from './pages/Home.vue'
  import axios from 'axios'
  import { ref, watch, provide, computed} from 'vue'

  const items = ref([])
  const cart = ref([])

  const drawerOpen = ref(false)

  const closeDrawer = () => {
    drawerOpen.value = false
  }

  const openDrawer = () => {
    drawerOpen.value = true
  }

  provide("closeDrawer", closeDrawer)

  const totalPrice = computed(
    () => cart.value.reduce((acc , item) => acc+item.price,0)
  )

  const toggleToCart = (item) => {
    if(!item.isAdded){
      cart.value.push(item)
      item.isAdded = true
    }
    else{
      cart.value.splice(
        cart.value.indexOf(item)
        ,1
      )
      item.isAdded = false
    }
  }

  watch(cart, ()=> {
    localStorage.setItem('cart' , JSON.stringify(cart.value))
  },{
    deep:true
  })

  provide("toggleToCart", toggleToCart)
  provide('cart' , cart)
  provide("closeDrawer" , closeDrawer)
</script>

<template>
  <div>
    <Drawer :total-price="totalPrice"  v-if="drawerOpen" @close-drawer="closeDrawer"/>
    <div class="w-4/5 mx-auto bg-white rounded-xl shadow-xl mt-14 pb-8">
      <Header :total-price="totalPrice" @open-drawer="openDrawer"/>
      <div class="p-10">
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>

