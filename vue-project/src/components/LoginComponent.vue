<template>
  <div>
    <h2>Iniciar Sesión</h2>
    <form @submit.prevent="iniciarSesion">
      <label>Email:</label>
      <input v-model="email" type="email" />

      <label>Contraseña:</label>
      <input v-model="password" type="password" />

      <button type="submit">Iniciar Sesión</button>
    </form>
    <p v-if="error">{{ error }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      password: '',
      error: null,
    };
  },
  methods: {
    async iniciarSesion() {
      try {
        const response = await fetch('/users.json');
        const usuarios = await response.json();

        const usuarioEncontrado = usuarios.find(
          (usuario) => usuario.email === this.email && usuario.password === this.password
        );

        if (usuarioEncontrado) {
          this.$emit('inicio-sesion-exitoso', usuarioEncontrado);
          sessionStorage.setItem('usuario', JSON.stringify(usuarioEncontrado));
        } else {
          this.error = 'Error al verificar el usuario. Email o contraseña incorrectos.';
        }
      } catch (error) {
        this.error = 'Error al verificar el usuario.';
        console.error('Error al cargar los usuarios', error);
      }
    },
  },
};
</script>
