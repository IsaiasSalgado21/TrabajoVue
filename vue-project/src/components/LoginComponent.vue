<template>
    <fieldset v-if="!logueado">
      <label>Email:</label>
      <input v-model="correo" placeholder="Email" />
      <label>Contraseña:</label>
      <input v-model="contraseña" placeholder="Contraseña" type="password" />
      <button @click="iniciarSesion">Acceder</button>
      <div v-if="mensaje">{{ mensaje }}</div>
    </fieldset>
  </template>
  
  <script>
  import { ref } from "vue";
  
  export default {
    name: "LoginComponent",
    emits: ["inicio-sesion-exitoso"],
    setup(props, { emit }) {
      let correo = ref("");
      let contraseña = ref("");
      let mensaje = ref("");
  
      const iniciarSesion = async () => {
        try {
          const response = await fetch("/users.json");
          const usuarios = await response.json();
          const usuarioValido = usuarios.find(
            (usuario) =>
              usuario.email === correo.value && usuario.password === contraseña.value
          );
  
          if (usuarioValido) {
            sessionStorage.setItem("usuario", JSON.stringify(usuarioValido));
            emit("inicio-sesion-exitoso", usuarioValido);
          } else {
            mensaje.value = "Correo o contraseña incorrectos.";
          }
  
          correo.value = "";
          contraseña.value = "";
        } catch (error) {
          mensaje.value = "Error al verificar el usuario.";
        }
      };
  
      return { correo, contraseña, mensaje, iniciarSesion };
    },
  };
  </script>
  