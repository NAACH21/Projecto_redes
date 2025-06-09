<template>
  <div class="container">
    <h1>Conversor de direcciones IP</h1>
    <p>Convierte fácilmente direcciones IPv4 a IPv6 y viceversa.</p>

    <div class="form-group">
      <label for="ip-input">Ingresa la dirección IP:</label>
      <input
        id="ip-input"
        type="text"
        v-model="ipInput"
        placeholder="Ejemplo: 192.168.0.1 o ::1"
      />
    </div>

    <div class="buttons">
      <button @click="convertToIPv6">Convertir a IPv6</button>
      <button @click="convertToIPv4">Convertir a IPv4</button>
    </div>

    <div class="result" v-if="conversionResult">
      <h3>Resultado:</h3>
      <p>{{ conversionResult }}</p>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  text-align: center;
  border: 1px solid #ddd;
  border-radius: 10px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

h1 {
  font-size: 2rem;
  color: #333;
}

p {
  color: #555;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 20px;
}

input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 1rem;
}

.buttons {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
}

button:hover {
  background-color: #0056b3;
}

.result {
  margin-top: 30px;
  padding: 15px;
  background-color: #e8f5e9;
  border: 1px solid #4caf50;
  border-radius: 5px;
  color: #2e7d32;
}
</style>

<script>
export default {
  name: "PageOne",
  data() {
    return {
      ipInput: "",
      conversionResult: "",
    };
  },
  methods: {
    convertToIPv6() {
      try {
        const parts = this.ipInput.split(".");
        if (
          parts.length !== 4 ||
          parts.some((part) => isNaN(part) || part < 0 || part > 255)
        ) {
          throw new Error("Dirección IPv4 inválida");
        }

        const ipv6 = parts
          .map((part) => parseInt(part).toString(16).padStart(2, "0"))
          .join("")
          .match(/.{1,4}/g)
          .join(":");

        this.conversionResult = `::ffff:${ipv6}`;
      } catch (error) {
        this.conversionResult = error.message;
      }
    },
    convertToIPv4() {
      try {
        const match = this.ipInput.match(
          /^::ffff:([0-9a-f]{1,4}):([0-9a-f]{1,4})$/i
        );
        if (!match) throw new Error("Dirección IPv6 inválida");

        const ipv4 = match
          .slice(1)
          .map((part) => parseInt(part, 16))
          .join(".");

        this.conversionResult = ipv4;
      } catch (error) {
        this.conversionResult = error.message;
      }
    },
  },
};
</script>
