<script setup>
    import DrawerHead from './DrawerHead.vue'
    import CartItemList from './CartItemList.vue'
    import InfoBlock from './InfoBlock.vue'
    import { computed, inject, provide, ref } from "vue";
    import axios from 'axios';

    const props = defineProps({
        totalPrice: Number,
    })
    const cart = inject("cart")
    const isCreatingOrder = ref(false)
    const orderId = ref(null)
    const emit = defineEmits(["closeDrawer" , 'createOrder'])

    const createOrder = async () => {
        try{
        isCreatingOrder.value = true
        const {data} = await axios.post('https://83fbe6250193ab17.mokky.dev/orders' , {
            items:cart.value,
            totalPrice: props.totalPrice
        })
        cart.value=[]
        orderId.value = data.id
        }
        catch(err){
        console.error(err)
        }
        finally{
        isCreatingOrder.value = false
        }
    }
    const buttonDisabled = computed(() => isCreatingOrder.value ? true : props.totalPrice ? false : true)
</script>

<template>
    <div>
        <div class="fixed top-0 left-0 h-full w-full bg-black z-2 opacity-70" @click="() => emit('closeDrawer')" ></div>
        <div class="bg-white h-full fixed right-0 top-0 z-3 w-96 p-8 flex flex-col" v-auto-animate>
            <DrawerHead @close-drawer="emit('closeDrawer')" />
            <div v-if="!totalPrice || orderId" class="flex flex-col justify-center h-full items-center">

                <InfoBlock
                    v-if="orderId"
                    title="Заказ оформлен"   
                    :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
                    image-url="/order-success-icon.png" 
                />


                <InfoBlock
                    v-if="!totalPrice && !orderId"
                    title="Корзина пустая"   
                    description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ"
                    image-url="/package-icon.png" 
                />
            </div>
            <CartItemList/>
            <div v-if="totalPrice" class="flex flex-col gap-4">
                <div class="flex gap-2">
                    <span>Итого:</span>
                    <div class="flex-1 border-b border-dashed border-gray-200"></div>
                    <b>{{totalPrice}} руб.</b>
                </div>
                <div class="flex gap-2">
                    <span>Налог(5%):</span>
                    <div class="flex-1 border-b border-dashed border-gray-200"></div>
                    <b>{{Math.round(totalPrice*0.05)}} руб.</b>
                </div>
            </div>
            <button
                v-if="totalPrice" 
                :disabled="buttonDisabled"
                @click="() => createOrder()"
                class="bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 transition active:bg-lime-700 disabled:bg-slate-300 mt-7"
            >
            {{isCreatingOrder ? "Заказ оформляется" : "Оформить заказ"}}
        </button>
        </div>
    </div>
</template>