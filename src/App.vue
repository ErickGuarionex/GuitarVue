<script setup>
    import {ref, reactive, onMounted, watch } from 'vue'
    import Header from './components/Header.vue'
    import {db} from './data/guitarras'
    import Footer from './components/Footer.vue'
    import Guitarra from './components/Guitarra.vue'

    const guitarras = ref([]);
    const carrito = ref([]);
    const guitarra = ref({});

    watch(carrito, () =>{
        guardarLocalStorage()
    }, {
        deep: true
    })
    onMounted( () =>{
        guitarras.value = db;
        guitarra.value = db[3]

        const carritoStorage = localStorage.getItem('carrito')
        if(carritoStorage){
            carrito.value = JSON.parse(carritoStorage)
        }
    })

    const guardarLocalStorage = () =>{
        localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }

    const agregarCarrito = (guitarra) =>{
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);

        if(existeCarrito >= 0){
            carrito.value[existeCarrito].cantidad++
        }else{
        guitarra.cantidad = 1;
        carrito.value.push(guitarra)
        }
        
    }

    const decrementar = (id) =>{
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad <= 1) return
        carrito.value[index].cantidad--;
    }
    const incrementar = (id) =>{
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++;
    }

    const eliminarProducto = (id) =>{
        carrito.value = carrito.value.filter( producto => producto.id !== id)
    }
    const vaciarCarrito = () =>{
        carrito.value = []
    }
</script>

<template>
    <Header 
    :guitarra="guitarra"
    :carrito="carrito"
    @incrementar="incrementar"
    @decrementar="decrementar"
    @agregar-carrito="agregarCarrito"
    @eliminarProducto="eliminarProducto"
    @vaciarCarrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">

            <Guitarra v-for="
            guitarra in guitarras"
            v-bind:guitarra="guitarra"
            @agregar-carrito="agregarCarrito"
            />

        </div>
        <Footer />
    </main>
</template>
