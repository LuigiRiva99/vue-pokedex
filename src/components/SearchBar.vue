<template>
    <div>
        <div>
            <input type="text" v-model="userInput">
            <button @click="search()">cerca</button>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
    export default {
        data() {
            return {
                userInput : '',
                pokemon: {},
            }
        },

        methods: {
            pkmnCall() {
                axios.get('https://pokeapi.co/api/v2/pokemon/',{
                    params: {
                    }
                }).then((res)=>{
                    
                })
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>