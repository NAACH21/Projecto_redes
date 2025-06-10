<template>
  <div class="container">
    <h1 class="title">Conversor de Direcciones IP</h1>
    <p class="subtitle">Transforma IPv4 a IPv6 y viceversa fácilmente</p>

    <div class="form-group">
      <input
        id="ip-input"
        class="input"
        type="text"
        v-model="ipInput"
        placeholder="Ingresa una dirección IPv4 o IPv6"
      />
      <p class="helper-text">Ejemplo: 192.168.0.1 o ::1</p>
    </div>

    <div class="buttons">
      <button class="button ipv6" @click="convertToIPv6">
        Convertir a IPv6
      </button>
      <button class="button ipv4" @click="convertToIPv4">
        Convertir a IPv4
      </button>
    </div>

    <transition name="fade">
      <div v-if="errorMessage" class="alert alert-error">
        {{ errorMessage }}
      </div>
    </transition>

    <transition name="fade">
      <div v-if="conversionResult" class="alert alert-success">
        <h3>Resultado:</h3>
        <p>{{ conversionResult }}</p>
      </div>
    </transition>
  </div>
</template>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap");

.container {
  max-width: 700px;
  margin: 50px auto;
  padding: 30px;
  text-align: center;
  border-radius: 15px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.2);
  font-family: "Roboto", sans-serif;
}

.title {
  font-size: 2.5rem;
  color: #fff;
  margin-bottom: 10px;
}

.subtitle {
  color: #f0f0f0;
  margin-bottom: 25px;
}

.form-group {
  margin-bottom: 20px;
}

.input {
  width: 100%;
  padding: 15px;
  border: none;
  border-radius: 10px;
  font-size: 1rem;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  outline: none;
}

.helper-text {
  margin-top: 5px;
  font-size: 0.9rem;
  color: #f0f0f0;
}

.buttons {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

.button {
  padding: 15px 25px;
  font-size: 1rem;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  color: #fff;
  transition: all 0.3s ease;
}

.button.ipv6 {
  background-color: #34d399;
}

.button.ipv6:hover {
  background-color: #059669;
}

.button.ipv4 {
  background-color: #60a5fa;
}

.button.ipv4:hover {
  background-color: #2563eb;
}

.alert {
  margin-top: 20px;
  padding: 15px;
  border-radius: 10px;
  font-size: 1rem;
}

.alert-success {
  background-color: #e6fffa;
  color: #065f46;
  border: 1px solid #34d399;
}

.alert-error {
  background-color: #fee2e2;
  color: #b91c1c;
  border: 1px solid #f87171;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>

<script>
export default {
  name: "IPConverter",
  data() {
    return {
      ipInput: "",
      conversionResult: "",
      errorMessage: "",
    };
  },
  methods: {
    validateIPv4(ip) {
      const parts = ip.split(".");
      if (parts.length !== 4)
        return {
          valid: false,
          error: "Debe contener 4 bloques separados por puntos.",
        };
      for (let i = 0; i < parts.length; i++) {
        if (isNaN(parts[i]) || parts[i] < 0 || parts[i] > 255) {
          return {
            valid: false,
            error: `El bloque ${i + 1} (${parts[i]}) es inválido.`,
          };
        }
      }
      return { valid: true };
    },
    validateIPv6(ip) {
      const pattern =
        /^([0-9a-f]{1,4}:){1,7}[0-9a-f]{1,4}$|^::(?:[0-9a-f]{1,4}:){0,5}[0-9a-f]{1,4}$|^::ffff:([0-9a-f]{1,4}):([0-9a-f]{1,4})$/i;
      if (!pattern.test(ip)) {
        return {
          valid: false,
          error:
            "Formato inválido. Comprueba los bloques y los separadores (:).",
        };
      }
      return { valid: true };
    },
    convertToIPv6() {
      this.clearMessages();
      const validation = this.validateIPv4(this.ipInput);
      if (!validation.valid) {
        this.errorMessage = validation.error;
        return;
      }
      const parts = this.ipInput
        .split(".")
        .map((part) => parseInt(part).toString(16).padStart(2, "0"));
      const ipv6 = `::ffff:${parts[0]}${parts[1]}:${parts[2]}${parts[3]}`;
      this.conversionResult = ipv6;
    },
    convertToIPv4() {
      this.clearMessages();
      const validation = this.validateIPv6(this.ipInput);
      if (!validation.valid) {
        this.errorMessage = validation.error;
        return;
      }

      const match = this.ipInput.match(
        /^::ffff:([0-9a-f]{2})([0-9a-f]{2}):([0-9a-f]{2})([0-9a-f]{2})$/i
      );
      if (!match) {
        this.errorMessage =
          "Formato de IPv6 no convertible a IPv4 o dirección inválida.";
        return;
      }

      const ipv4 = match
        .slice(1)
        .map((part) => parseInt(part, 16))
        .join(".");
      this.conversionResult = ipv4;
    },
    clearMessages() {
      this.conversionResult = "";
      this.errorMessage = "";
    },
  },
};
</script>
