<template>
    <div>
      <h2>Añadir nuevo Usuario</h2>
      <form @submit.prevent="guardarUsuario">
        <label>Nombre:</label>
        <input v-model="nuevoUsuario.name" />
  
        <label>Teléfono:</label>
        <input v-model="nuevoUsuario.phone" />
  
        <label>Empresa:</label>
        <input v-model="nuevoUsuario.company.name" />
  
        <button type="submit">Guardar</button>
        <button type="button" @click="$emit('cancelar')">Cancelar</button>
      </form>
    </div>
  </template>
  
  <script>
  export default {
    name: 'AñadirUsuario',
    data() {
      return {
        nuevoUsuario: { name: '', phone: '', company: { name: '' } },
      };
    },
    methods: {
      guardarUsuario() {
        this.$emit('guardar-usuario', { ...this.nuevoUsuario, id: Date.now() });
        this.nuevoUsuario = { name: '', phone: '', company: { name: '' } };
      },
    },
  };
  </script>
  