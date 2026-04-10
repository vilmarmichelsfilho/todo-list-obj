<script setup>
import { ref, computed } from 'vue';
import taskChild from './components/taskChild.vue';

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
      <taskChild v-for="item in tarefas"
      :key="item.id"
      :desc="item.desc"
      :status="item.status"
      :id="item.id"
      @remover="deletar"
      @editar="editar"
      @marcar="marcarConcluida"
      ></taskChild>
    </ul>
    <input type="text" placeholder="Filtrar tarefa" v-model="filtro">
    <button v-on:click="tarefas.sort((a, b) => a.desc.localeCompare(b.desc, 'pt-BR'))">Ordenar</button>
    <div class="contagem">
      <p>✅: {{ concluidas }}</p>
      <p>🚫: {{ pendentes }}</p>
    </div>
  </div>
</template>

<style scoped>

.contagem {
  display: flex;
  justify-content: space-between;
  margin: 1rem 3rem 0 3rem;
}

.container {
  background-color: rgb(29, 29, 29);
  padding: 2rem;
  box-shadow: 1px 1px 3px 3px rgb(15, 15, 15);
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

.concluida {
  text-decoration: line-through;
}
</style>
