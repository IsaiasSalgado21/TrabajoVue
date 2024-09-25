<template>
  <div id="app">
    <LoginComponent v-if="!logueado" @inicio-sesion-exitoso="manejarInicioSesionExitoso" />
    <div v-if="logueado">
      <h1>¡Bienvenido, {{ usuarioActual.name }}!</h1>
      <button @click="cerrarSesion">Cerrar sesión</button>
      <UserTableComponent :usuarios="usuarios" 
                          @eliminar-usuario="eliminarUsuario" 
                          @seleccionar-usuario-para-editar="seleccionarUsuarioEditar" />
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
import { ref, onMounted } from "vue";
import LoginComponent from "./components/LoginComponent.vue";
import UserTableComponent from "./components/UserTableComponent.vue";

export default {
  name: "App",
  components: {
    LoginComponent,
    UserTableComponent,
  },
  setup() {
    let logueado = ref(false);
    let usuarioActual = ref(null);
    let usuarios = ref([]);
    let usuarioEditando = ref(null);

    const cargarUsuarios = async () => {
      try {
        const response = await fetch("/users.json");
        usuarios.value = await response.json();
      } catch (error) {
        console.error("Error al cargar los usuarios", error);
      }
    };

    onMounted(() => {
      if (sessionStorage.getItem("usuario")) {
        logueado.value = true;
        usuarioActual.value = JSON.parse(sessionStorage.getItem("usuario"));
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
      sessionStorage.removeItem("usuario");
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

    return {
      logueado,
      usuarioActual,
      usuarios,
      usuarioEditando,
      manejarInicioSesionExitoso,
      cerrarSesion,
      eliminarUsuario,
      seleccionarUsuarioEditar,
      guardarCambiosUsuario,
      cancelarEdicion,
    };
  },
};
</script>
