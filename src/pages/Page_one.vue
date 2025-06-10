<template>
  <div class="container">
    <h1 class="title">Conversor de Direcciones IP</h1>
    <p class="subtitle">
      Convierte entre formatos IPv4 e IPv6 de manera sencilla
    </p>

    <div class="form-group">
      <input
        id="ip-input"
        class="input"
        type="text"
        v-model="ipInput"
        placeholder="Ejemplo: 192.168.0.1 o ::1"
      />
      <p class="helper-text">Ingresa una dirección IPv4 o IPv6 válida</p>
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
.container {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  text-align: center;
  border-radius: 10px;
  background: linear-gradient(135deg, #ff9a9e, #fad0c4);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
}

.title {
  font-size: 2.5rem;
  color: #fff;
  margin-bottom: 10px;
}

.subtitle {
  color: #fcefe8;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 20px;
}

.input {
  width: 100%;
  padding: 12px;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.helper-text {
  margin-top: 5px;
  font-size: 0.9rem;
  color: #fff;
}

.buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.button {
  padding: 10px 20px;
  font-size: 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  color: #fff;
  transition: all 0.3s ease;
}

.button.ipv6 {
  background-color: #4caf50;
}

.button.ipv6:hover {
  background-color: #388e3c;
}

.button.ipv4 {
  background-color: #2196f3;
}

.button.ipv4:hover {
  background-color: #1976d2;
}

.alert {
  margin-top: 20px;
  padding: 15px;
  border-radius: 5px;
  font-size: 1rem;
}

.alert-success {
  background-color: #e8f5e9;
  color: #2e7d32;
  border: 1px solid #4caf50;
}

.alert-error {
  background-color: #ffebee;
  color: #d32f2f;
  border: 1px solid #f44336;
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
      return (
        parts.length === 4 &&
        parts.every((part) => !isNaN(part) && +part >= 0 && +part <= 255)
      );
    },
    validateIPv6(ip) {
      return (
        /^([0-9a-f]{1,4}:){1,7}[0-9a-f]{1,4}$/i.test(ip) ||
        /^::ffff:([0-9a-f]{1,4}):([0-9a-f]{1,4})$/i.test(ip)
      );
    },
    convertToIPv6() {
      this.clearMessages();
      if (!this.validateIPv4(this.ipInput)) {
        this.errorMessage = "Por favor, ingresa una dirección IPv4 válida.";
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
      if (!this.validateIPv6(this.ipInput)) {
        this.errorMessage = "Por favor, ingresa una dirección IPv6 válida.";
        return;
      }
      const match = this.ipInput.match(
        /^::ffff:([0-9a-f]{1,4}):([0-9a-f]{1,4})$/i
      );
      if (!match) {
        this.errorMessage = "Formato de IPv6 no convertible a IPv4.";
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
