    <template>
        <div class="flex flex-col justify-center items-center w-screen h-screen gap-2 bg-slate-400 ">
            <div v-if="bemVindo" >
                <h1 class="pb-4 text-red-800 font-bold" >Bem vindo ao Sorteio!</h1>
                <button @click="bemVindo = false"
                class="h-16 w-40
                        bg-red-800
                        hover:bg-red-900
                        font-bold
                        text-white
                        rounded">Começar Apostas</button>
            </div>
            <div v-else class="flex gap-4 items-center w-9/12">
                <Aposta @emitAposta="setAposta" />
                <button v-if="jogadores.length > 0" @click="sorteio" class="h-16 w-40
                    bg-red-800 
                    hover:bg-red-900
                    font-bold
                    text-white 
                    rounded">Finalizar Apostas
                </button>
            </div>
        </div>
    </template>

    <script setup lang="ts">

    let bemVindo = ref(true)
    const jogadores: Jogador[] = reactive([]); //array dos apostadores

    class Jogador { //classe de jogador
        cpf: number
        jogos: number[][]
        
        constructor(cpf: number, jogo: number[]) {
            this.cpf = cpf
            this.jogos = [jogo]
        }
    }

    const setAposta = (cpf: number, jogo: number[]) => { //função que foi emitida no elemento child
        const jogadorExistente = jogadores.find(jogador => jogador.cpf === cpf); //verifica se cpf ja foi cadastrado

        if (jogadorExistente) { //se ja foi, adiciona jogada ao jogador que ja existe
            jogadorExistente.jogos.push(jogo)
            console.log('Jogo adicionado ao jogador: ', jogadorExistente.cpf);
        } else { //se não, cria novo jogador
            apostasTeste()
            let j = new Jogador(cpf, jogo)
            jogadores.push(j)
            console.log('Jogador adicionado', j);
        }
    }

    const sorteio = () => {
        localStorage.setItem('jogadores', JSON.stringify(jogadores));//usa localStorage pra passar os dados
        navigateTo('/sorteio') //direciona pra pagina de sorteio       
    }


    const apostasTeste = () => { // gera jogadores e apostas automáticas para teste/demonstração
        for (let i = 0; i < 5; i++) { // gera 5 jogadores diferentes
            let min = 10000000000;
            let max = 99999999999;
            let cpfAleatorio = Math.floor(Math.random() * (max - min + 1)) + min; // CPF aleatório
            
            for (let j = 0; j < 2; j++) { // duas apostas para cada jogador
                let jogoAleatorio: Set<number> = new Set(); // Set para evitar numeros repetidos
                while (jogoAleatorio.size < 5) { // Garante que haja 5 números distintos
                    jogoAleatorio.add(Math.floor(Math.random() * 50) + 1);
                }
                let j = new Jogador(cpfAleatorio, Array.from(jogoAleatorio));
                jogadores.push(j);
            }
        }
    }

    </script>