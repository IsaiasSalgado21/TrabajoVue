<template>
    <div v-if="usuarios.length">
      <h1>Usuarios Registrados</h1>
      <table border="1">
        <thead>
          <tr>
            <th>Nombre</th>
            <th>Teléfono</th>
            <th>Empresa</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="usuario in usuarios" :key="usuario.id">
            <td>{{ usuario.name }}</td>
            <td>{{ usuario.phone }}</td>
            <td>{{ usuario.company.name }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    name: "UserTableComponent",
    props: ["usuarios"],
  };
  </script>
  