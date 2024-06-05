<template>
  <div class="flex items-center bg-slate-800 min-h-screen pl-14">
    <div>
      <h1 class="font-bold text-2xl text-white">Apostas</h1>
      <table class="table-fixed border-separate w-92 bg-slate-800">
        <thead>
          <tr>
            <th class="border-1 p-2 border border-slate-500 bg-blue-500 text-white">CPF</th>
            <th class="border-1 p-2 border border-slate-500 bg-blue-500 text-white">APOSTAS</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="jogador in jogadores" :key="jogador.cpf" class=" text-white">
            <td class="border-1 p-2 border border-slate-500">{{ jogador.cpf }}</td>
            <td class="border-1 p-2 border border-slate-500">
              <ul>
                <li v-for="(jogo, index) in jogador.jogos" :key="index" :class="{ 'jogo': jogoVencedorClass(jogo) }">
                  {{ jogo.sort((a: number, b: number) => a - b).join(', ') }}
                </li>
              </ul>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="flex flex-col gap-2 p-10">
      <p v-if="semVencedor" class="text-white">Sem vencedores</p>
      <div v-if="numerosSorteados.length != 0">
        <h2 class="font-bold text-xl mb-4 text-white">Números Sorteados</h2>
        <ul class="flex">
          <li class="bg-blue-500 border border-separate border-slate-800 p-2"
            v-for="(numero, index) in numerosSorteados" :key="index" :class="{ 'jogo': jogoVencedor.includes(numero) }">
            {{ numero }}
          </li>
        </ul>
      </div>
      <div class="flex gap-2">
        <button v-if="jogadores.length > 0" @click="sortear" class="h-12 w-24
                bg-lime-600
                hover:bg-lime-700
                font-bold
                text-white
                rounded">SORTEAR!
        </button>
        <button v-if="jogadores.length > 0" @click="voltar" class="h-12 w-24
                bg-red-800
                hover:bg-red-900
                font-bold
                text-white
                rounded">Voltar
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const jogadores = JSON.parse(localStorage.getItem('jogadores') || '[]')//carrega jogadores
const numerosSorteados = ref<number[]>([])
let vencedor: { cpf: string; jogos: any[]; }
let jogoVencedor: number[]
let semVencedor = ref(false)

function sortear() {
  numerosSorteados.value = [] //limpa pra poder testar novos sorteios

  while (numerosSorteados.value.length < 5) {//sorteia os primeiros 5 juntos
    let n = Math.floor(Math.random() * 50) + 1
    if (!numerosSorteados.value.includes(n)) {//garante que nao vai repetir
      numerosSorteados.value.push(n);//eu ia fazer com Set mas dai prejudica a 'reatividade' então fiz assim mais simples
    }
  }

  vencedor = jogadores.find((jogador: { jogos: any[]; }) => {//retorna true se alguem venceu
    return jogador.jogos.find(jogo => {//verifica cada jogador
      return jogo.every((numero: number) => numerosSorteados.value.includes(numero))//e cada jogo deste jogador
    })
  })

  while (!vencedor && numerosSorteados.value.length < 25) {//se ninguem vencer com 5 nums soreados
    let n = Math.floor(Math.random() * 50) + 1;//sera sorteado um numero de cada ve até alguem vencer
    if (!numerosSorteados.value.includes(n)) {//verifica e evita possivel num repetido
      numerosSorteados.value.push(n);
    }


    vencedor = jogadores.find((jogador: { jogos: any[]; }) => {//verifica se ouve vencedor
      return jogador.jogos.find(jogo => {//mas agora a cada novo num sorteado
        return jogo.every((numero: number) => numerosSorteados.value.includes(numero))
      });
    });
  }

  if (vencedor) {//se alguem venceu entra aqui
    console.log('CPF do jogador vencedor: ' + vencedor.cpf); //teste
    jogoVencedor = vencedor.jogos.find((jogo: any[]) => {//acha o jogo que venceu deste apostador
      return jogo.every((numero: number) => numerosSorteados.value.includes(numero))
    })
    semVencedor.value = false//controle de elemento do DOM
    console.log('Jogo vencedor: ' + jogoVencedor.sort((a, b) => a - b));//teste
  } else {
    semVencedor.value = true//controle DOM
    numerosSorteados.value = [];//1controle DOM
    console.log('Não há vencedores.');//teste
  }
}

function jogoVencedorClass(jogo: number[]): boolean {//func para adicionar classe a li que apresenta o jogo vencedor
  return jogoVencedor && jogoVencedor.every(numero => jogo.includes(numero));
}

function voltar() {
  navigateTo('/')
}
</script>
