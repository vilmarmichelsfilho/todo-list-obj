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
      let maiorNumero = Math.max(...tarefas.value.map(item => item.id));
      tarefas.value.push({ id: maiorNumero + 1, desc: novaDesc.value, status: 'pendente' });
      novaDesc.value = '';
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
  let posicao = tarefas.value.findIndex(item => item.id === id);
  tarefas.value.splice(posicao, 1);
}

function editar(id) {
  let position = tarefas.value.findIndex(item => item.id === id);
  posicao.value = position;
  novaDesc.value = tarefas.value[position].desc
}

</script>

<template>
  <div class="container">
    <h1>Tarefas</h1>
    <div class="botao">
      <input type="text" v-model="novaDesc" placeholder="Insira uma nova tarefa" @keyup.enter="inserirTarefa">
      <button v-on:click="inserirTarefa" :disabled="novaDesc.length < 5"><span v-show="posicao === -1">Inserir</span><span
          v-show="posicao != -1">Editar</span></button>
    </div>
    <ul>
      <li v-for="item in tarefasFiltradas" :key="item.id">
        <span v-on:click="marcarConcluida(item.id)" :class="{ concluida: item.status === 'concluida' }">{{ item.desc
          }}</span>
        <a v-on:click="deletar(item.id)">Del</a>
        <a v-on:click="editar(item.id)">Edit</a>
      </li>
    </ul>
    <input type="text" placeholder="Filtrar tarefa" v-model="filtro">
    <button v-on:click="tarefas.sort((a, b) => a.desc.localeCompare(b.desc, 'pt-BR'))">Ordenar</button>
    <div class="contagem">
      <p>Pendentes: {{ pendentes }}</p>
      <p>Concluidas: {{ concluidas }}</p>
    </div>
  </div>
</template>

<style scoped>
.container ul li {
  cursor: pointer;
}

.concluida {
  text-decoration: line-through;
}
</style>
