<template>
  <div id="app">
    <LoginComponent v-if="!logueado" @inicio-sesion-exitoso="manejarInicioSesionExitoso" />
    <div v-if="logueado">
      <h1 class="titulo">¡Bienvenido, {{ usuarioActual.name }}!</h1>
      <button @click="cerrarSesion" class="boton">Cerrar sesión</button>
      <button @click="mostrarFormularioAñadir" class="botonAñadir">Añadir usuario</button>
      <UserTableComponent
        v-if="!añadiendoUsuario"
        :usuarios="usuarios"
        @eliminar-usuario="eliminarUsuario"
        @seleccionar-usuario-para-editar="seleccionarUsuarioEditar"
      />
      <AñadirUsuario
        v-if="añadiendoUsuario"
        @guardar-usuario="guardarNuevoUsuario"
        @cancelar="añadiendoUsuario = false"
      />
    </div>

    <div v-if="usuarioEditando">
      <h2>Editar usuario: {{ usuarioEditando.name }}</h2>
      <form @submit.prevent="guardarCambiosUsuario">
        <label>Nombre:</label>
        <input v-model="usuarioEditando.name" />

        <label>Teléfono:</label>
        <input v-model="usuarioEditando.phone" />

        <label>Empresa:</label>
        <input v-model="usuarioEditando.company.name" />

        <button type="submit">Guardar</button>
        <button type="button" @click="cancelarEdicion">Cancelar</button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import LoginComponent from './components/LoginComponent.vue';
import UserTableComponent from './components/UserTableComponent.vue';
import AñadirUsuario from './components/AñadirUsuario.vue';

export default {
  name: 'App',
  components: {
    LoginComponent,
    UserTableComponent,
    AñadirUsuario,
  },
  setup() {
    const logueado = ref(false);
    const usuarioActual = ref(null);
    const usuarios = ref([]);
    const usuarioEditando = ref(null);
    const añadiendoUsuario = ref(false);

    const cargarUsuarios = async () => {
      try {
        const response = await fetch('/users.json');
        usuarios.value = await response.json();
      } catch (error) {
        console.error('Error al cargar los usuarios', error);
      }
    };

    onMounted(() => {
      if (sessionStorage.getItem('usuario')) {
        logueado.value = true;
        usuarioActual.value = JSON.parse(sessionStorage.getItem('usuario'));
        cargarUsuarios();
      }
    });

    const manejarInicioSesionExitoso = (usuario) => {
      logueado.value = true;
      usuarioActual.value = usuario;
      cargarUsuarios();
    };

    const cerrarSesion = () => {
      logueado.value = false;
      usuarioActual.value = null;
      usuarios.value = [];
      sessionStorage.removeItem('usuario');
    };

    const eliminarUsuario = (id) => {
      usuarios.value = usuarios.value.filter((usuario) => usuario.id !== id);
    };

    const seleccionarUsuarioEditar = (usuario) => {
      usuarioEditando.value = { ...usuario };
    };

    const guardarCambiosUsuario = () => {
      const index = usuarios.value.findIndex((u) => u.id === usuarioEditando.value.id);
      if (index !== -1) {
        usuarios.value[index] = { ...usuarioEditando.value };
        usuarioEditando.value = null;
      }
    };

    const cancelarEdicion = () => {
      usuarioEditando.value = null;
    };

    const mostrarFormularioAñadir = () => {
      añadiendoUsuario.value = true;
      usuarioEditando.value = null;
    };

    const guardarNuevoUsuario = (nuevoUsuario) => {
      usuarios.value.push(nuevoUsuario);
      añadiendoUsuario.value = false;
    };

    return {
      logueado,
      usuarioActual,
      usuarios,
      usuarioEditando,
      añadiendoUsuario,
      manejarInicioSesionExitoso,
      cerrarSesion,
      eliminarUsuario,
      seleccionarUsuarioEditar,
      guardarCambiosUsuario,
      cancelarEdicion,
      mostrarFormularioAñadir,
      guardarNuevoUsuario,
    };
  },
};
</script>

<style scoped>
.titulo {
  color: blueviolet;
  font-size: 48px;
}

.boton {
  background-color: red;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
}
.botonAñadir {
  background-color: green;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
}
</style>
