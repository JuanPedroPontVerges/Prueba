<template>
  <div class="contenedor" v-if="!logeado">
    <h1>Login</h1>
    <div class="form">
      <label for="usuario">Usuario:</label>
      <input type="text" id="usuario" v-model="formData.user" />
      <label for="contrasena">Contraseña:</label>
      <input type="password" id="contrasena" v-model="formData.password" />
      <div>
        <button @click="login(formData.user, formData.password)">
          Ingresar
        </button>
      </div>
    </div>
  </div>
  <div v-else>
    <h1>LOGEADO</h1>
  </div>
</template>

<script lang="ts">

import { Token } from "./Token";
export default defineComponent({
  name: "App",
  components: {},
  data() {
    return {
      token: { token: "", tiempoExpiracion: 0 } as Token,
      logeado: false,
      formData: {
        user: "",
        password: "",
      },
    };
  },
  methods: {
    async getToken() {
      const response = await fetch("http://173.212.232.163:81/api/token", {
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
        },
        method: "POST",
        body: JSON.stringify({ usuario: "string", clave: "string" }),
      });
      this.token = await response.json();
    },

    async login(usuario: string, clave: string) {
      await this.getToken();
      const response = await fetch(
        "http://173.212.232.163:81/api/usuario/login",
        {
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
            Authorization: this.token.token,
          },
          method: "POST",
          body: JSON.stringify({ usuario, clave }),
        }
      );
      const respo = await response.json();
      if (respo.codigo == 200) {
        this.logeado = true;
        console.log("estado", this.logeado);
      }
      console.log(respo);
    },
  },
});
</script>

<style>
h1 {
  text-align: center;
}
.contenedor {
  max-width: 800px;
  margin: 40px auto;
}

.form label {
  font-size: 30px;
  margin: 20px;
}

.form input {
  max-width: 300px;
}

.form button {
  font-size: 22px;
  padding: 10px;
  margin: 10px;
  text-align: right;
}
</style>
