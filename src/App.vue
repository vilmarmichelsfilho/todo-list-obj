<script setup>
import { ref, computed } from 'vue';

const novaDesc = ref('');
let posicao = ref(-1);
let filtro = ref('');
let tarefas = ref([
  { id: 1, desc: 'Estudar VueJS', status: 'pendente' },
  { id: 2, desc: 'Fazer todo-list', status: 'pendente' },
  { id: 3, desc: 'Deploy contador Vue', status: 'concluida' }
])

let pendentes = computed(() => {
  return tarefas.value.filter(item => item.status.includes('pendente')).length;
})
let concluidas = computed(() => {
  return tarefas.value.filter(item => item.status.includes('concluida')).length;
})

let tarefasFiltradas = computed(() => {
  if (filtro.value.trim().length > 0) {
    return tarefas.value.filter(item => item.desc.toLowerCase().includes(filtro.value.toLowerCase()));
  } else {
    return tarefas.value;
  }
})

function inserirTarefa() {
  if (novaDesc.value.length >= 5) {
    if (posicao.value == -1) {
      if (tarefas.value.length <= 0) {
        tarefas.value.push({ id: 1, desc: novaDesc.value, status: 'pendente' });
        novaDesc.value = '';
      } else {
        let maiorNumero = Math.max(...tarefas.value.map(item => item.id));
        tarefas.value.push({ id: maiorNumero + 1, desc: novaDesc.value, status: 'pendente' });
        novaDesc.value = '';
      }
    } else {
      tarefas.value[posicao.value].desc = novaDesc.value;
      novaDesc.value = '';
      posicao.value = -1;
    }

  }
}

function marcarConcluida(id) {
  let posicao = tarefas.value.findIndex(item => item.id === id);
  if (tarefas.value[posicao].status === 'concluida') {
    tarefas.value[posicao].status = 'pendente';
  } else {
    tarefas.value[posicao].status = 'concluida';
  }
}

function deletar(id) {
  let posicao2 = tarefas.value.findIndex(item => item.id === id);
  tarefas.value.splice(posicao2, 1);
  novaDesc.value = '';
  posicao.value = -1;
}

function editar(id) {
  let position = tarefas.value.findIndex(item => item.id === id);
  posicao.value = position;
  novaDesc.value = tarefas.value[position].desc
}

</script>

<template>
  <div class="container">
    <h1>TAREFAS</h1>
    <div class="botao">
      <input type="text" v-model="novaDesc" placeholder="Insira uma nova tarefa" @keyup.enter="inserirTarefa">
      <button v-on:click="inserirTarefa" :disabled="novaDesc.length < 5"><span
          v-show="posicao === -1">Inserir</span><span v-show="posicao != -1"
          style="font-weight: bold;">Editar</span></button>
    </div>
    <ul>
      <li v-for="item in tarefasFiltradas" :key="item.id">
        <span v-on:click="marcarConcluida(item.id)" :class="{ concluida: item.status === 'concluida' }">{{ item.desc
        }}</span>
        <a v-on:click="editar(item.id)" class="edit">✏️</a>
        <a v-on:click="deletar(item.id)" class="deleti">🗑️</a>
      </li>
    </ul>
    <input type="text" placeholder="Filtrar tarefa" v-model="filtro">
    <button v-on:click="tarefas.sort((a, b) => a.desc.localeCompare(b.desc, 'pt-BR'))">Ordenar</button>
    <div class="contagem">
      <p>✅: {{ pendentes }}</p>
      <p>❌: {{ concluidas }}</p>
    </div>
  </div>
</template>

<style scoped>
.container ul li {
  cursor: pointer;
}

.contagem {
  display: flex;
  justify-content: space-between;
  margin: 1rem 3rem 0 3rem;
}

.container {
  background-color: white;
  padding: 2rem;
  box-shadow: 1px 1px 3px 3px rgb(146, 146, 146);
  border-radius: 15px;
}

.container h1 {
  text-align: center;
  font-weight: bold;
}

.container ul {
  list-style: none;
  padding: 0;
}

.container ul li {
  margin: 5px 0;
}

.container input {
  border-radius: 5px;
  border: none;
  background-color: rgb(219, 219, 219);
  padding: 4px;
  margin: 5px;
  box-shadow: 1px 1px 1px 1px rgb(146, 146, 146);
}

.container button {
  border: none;
  cursor: pointer;
  background-color: rgb(152, 207, 152);
  padding: 4px 10px;
  border-radius: 5px;
  box-shadow: 1px 1px 1px 1px rgb(146, 146, 146);
  font-weight: bold;
}

.container ul li a {
  background-color: rgb(219, 219, 219);
  padding: 2px;
  margin: 2px;
  border-radius: 2px;
  box-shadow: 1px 1px 1px 1px rgb(146, 146, 146);
  transition: background-color 0.3s ease-out, box-shadow 0.3s ease-out;
}

.deleti:hover {
  background-color: rgb(155, 0, 0);
  box-shadow: 1px 1px 1px 1px rgb(100, 0, 0);
}

.edit:hover {
  background-color: rgb(15, 185, 0);
  box-shadow: 1px 1px 1px 1px rgb(10, 124, 0);
}

.concluida {
  text-decoration: line-through;
}
</style>
