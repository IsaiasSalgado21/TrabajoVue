<template>
    <div v-if="usuarios.length">
      <h1>Usuarios Registrados</h1>
      <table border="1">
        <thead>
          <tr>
            <th>Nombre</th>
            <th>Teléfono</th>
            <th>Empresa</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="usuario in usuarios" :key="usuario.id">
            <td>{{ usuario.name }}</td>
            <td>{{ usuario.phone }}</td>
            <td>{{ usuario.company.name }}</td>
            <td>
              <button @click="eliminarUsuario(usuario.id)"> eliminar</button>
              <button> editar </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    name: "UserTableComponent",
    props: ["usuarios"],
    methods:{
      eliminarUsuario(id){
        this.$emit("eliminar-usuario",id);
      },
    },
  };
  </script>
  