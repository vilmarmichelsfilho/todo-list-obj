<script setup>
defineProps(['desc', 'status', 'id'])

defineEmits(['marcar', 'remover', 'editar'])
</script>

<template>
    <li>
        <span @click="$emit('marcar', id)" :class="{ concluida: status === 'concluida' }">
            {{ desc}}
        </span>
        <a @click="$emit('editar', id)" class="edit">✏️</a>
        <a @click="$emit('remover', id)" class="deleti">🗑️</a>
    </li>
</template>

<style scoped>
li {
  cursor: pointer;
  margin: 5px 0;
}

li a {
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