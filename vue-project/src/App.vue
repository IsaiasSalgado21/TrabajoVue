<template>
  <div id="app">
    <LoginComponent v-if="!logueado" @inicio-sesion-exitoso="manejarInicioSesionExitoso" />
    <div v-if="logueado">
      <h1>¡Bienvenido, {{ usuarioActual.name }}!</h1>
      <button @click="cerrarSesion">Cerrar sesión</button>
      <UserTableComponent :usuarios="usuarios" />
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

    return { logueado, usuarioActual, usuarios, manejarInicioSesionExitoso, cerrarSesion };
  },
};
</script>
