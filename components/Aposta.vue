<template>
    <div>
        <h1>Marque 5 numeros</h1>
        <div class="grid grid-cols-10 gap-2">
            <div class="flex justify-center items-center w-16 h-16 bg-blue-500 hover:bg-blue-700 rounded cursor-pointer"
                v-for="numero in numeros" @click="marcaNumero(numero)"
                :class="{ 
                'marcado': marcados.includes(numero), 
                'desabilitado': marcados.length >= 5
                }">{{ numero }}
            </div>
        </div>

        <form @submit.prevent="getAposta" class="flex gap-2 pt-2 w-48">
            <input class="p-2 rounded" type="text" id="cpf" name="cpf" v-model="cpf" 
            placeholder="CPF" autofocus required pattern="^\d{11}$" 
            oninvalid="this.setCustomValidity('Insira exatamente 11 dígitos')" 
            oninput="this.setCustomValidity('')">
            <button class="btn" type="submit" v-if="marcados.length == 5">Apostar</button>
        </form>
    </div>
</template>

<script setup lang="ts">
const emit = defineEmits(['emitMarcados', 'emitAposta', 'finalizarApostas']) //configura emits

//---------------------------------------------GRID DE APOSTA---------------------------------------------//
const numeros: number[] = []; //array dos numeros para jogar e exibir DOM
let marcados: number[] = reactive([])//array dos numers marcados e garante que ta reativo
let finalizandoApostas = ref(false)

for (let i = 1; i <= 50; i++) { //armazena 1 a 50 em cada index      
    numeros.push(i)
}

const marcaNumero = (numero: number) => { //adiciona numero selec no array
    marcados.push(numero)//adiciona numero marcado no array
};

//---------------------------------------------FORM DE APOSTA---------------------------------------------//
const cpf = ref(null)  //recebe os campos do form
const getAposta = () => {
    console.log('CPF enviado');
    const jogo = marcados.slice()
    emit('emitAposta', cpf.value, jogo)
    marcados.splice(0)
}

</script>