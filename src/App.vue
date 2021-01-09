<template>
  <div id="app">
    <div class="contenedor">
      <div class="formulario">
        <h1 class="title is-1">Generar código QR Wifi</h1>
        <input
          class="input"
          type="text"
          placeholder="Introduce el nombre del Wifi"
          id="nombreID"
          v-model="nombreWifi"
        />
        <p id="errorNombre" class="error">{{ this.errorNombre }}</p>
        <input
          class="input"
          type="text"
          placeholder="Contraseña del Wifi"
          id="passID"
          v-model="contraseyawifi"
        />
        <p id="errorPass" class="error">{{ this.errorPass }}</p>
        <h4 class="subtitle is-4">Tipo de cifrado:</h4>
        <div class="control">
          <label class="radio">
            <input
              type="radio"
              name="TipoWifi"
              id="wpeID"
              v-model="tipoCifrado"
              value="WPE"
            />
            WPE
          </label>
          <label class="radio">
            <input
              type="radio"
              name="TipoWifi"
              id="wpaID"
              v-model="tipoCifrado"
              value="WPA"
            />
            WPA
          </label>
          <label class="radio">
            <input
              type="radio"
              name="TipoWifi"
              id="noneID"
              v-model="tipoCifrado"
              value="None"
            />
            Sin cifrado
          </label>
        </div>
        <p id="errorCifrado" class="error">{{ this.errorCifrado }}</p>
        <h4 class="subtitle is-4">¿Wifi oculto?</h4>

        <label class="checkbox">
          <input type="checkbox" id="visibilidadID" v-model="wifiOculto" />
          Oculto
        </label>
        <br />
        <button class="button is-link" id="generarID" @click="fcomprobar">
          Generar
        </button>
      </div>
      <div id="codigoqr">{{ this.codigoGenerado }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function () {
    return {
      codigoGenerado: "",
      contraseyawifi: "",
      nombreWifi: "",
      tipoCifrado: undefined,
      wifiOculto: false,

      //Errores
      errorNombre: "",
      errorPass: "",
      errorCifrado: "",
    };
  },
  methods: {
    fcomprobar() {
      this.flimpiar();
      let errores = false;

      const ExpresionPass = /^.+$/;
      const ExpresionNombre = /^\w+$/;

      if (this.tipoCifrado === undefined) {
        errores = true;
        this.errorCifrado = "Debes seleccionar un tipo de cifrado";
      }

      if (!ExpresionNombre.test(this.nombreWifi)) {
        errores = true;
        this.errorNombre = "Debes poner un nombre";
      }

      if (!ExpresionPass.test(this.contraseyawifi)) {
        errores = true;
        this.errorPass = "Debes poner una contraseña";
      }

      if (!errores) {
        this.fgenerar(
          this.nombreWifi,
          this.contraseyawifi,
          this.tipoCifrado,
          this.wifiOculto
        );
      }
    },
    fgenerar(ssid, pass, encriptado, visibilidad) {
      const qrcode = require("wifi-qr-code-generator");
      const pr = qrcode.generateWifiQRCode({
        ssid: ssid,
        password: pass,
        encryption: encriptado,
        hiddenSSID: visibilidad,
        outputFormat: { type: "image/png" },
      });
      pr.then(
        (data) => (
          (document.getElementById("codigoqr").innerHTML = `<img src=${data}>`),
          this.flimpiarFormu()
        )
      );
    },
    flimpiar() {
      this.errorNombre = "";
      this.errorPass = "";
      this.errorCifrado = "";
    },
    flimpiarFormu() {
      this.contraseyawifi = "";
      this.nombreWifi = "";
      this.tipoCifrado = undefined;
    },
  },
};
</script>

<style>
body {
  display: flex;
  width: 60%;
  height: 100%;
  margin: auto;
  flex-direction: column;
}
.contenedor {
  flex-direction: row;
  display: flex;
  align-items: center;
  align-self: center;
  align-content: center;
  margin-top: 10%;
}

.formulario {
  display: flex;
  flex-direction: column;
}

input {
  margin-top: 10px;
  margin-bottom: 20px;
}

#generarID {
  width: max-content;
}

.error {
  color: red;
}

img {
  margin-left: 50px;
  width: 200px;
  height: 200px;
}

@media (max-width: 600px) {
  img {
    width: auto;
    height: auto;
  }
}

@media (max-width: 400px) {
  .contenedor {
    flex-direction: column;
    align-content: center;
    align-items: center;
    align-self: center;
  }

  img {
    margin-left: 0;
    margin: auto;
  }
}
</style>
