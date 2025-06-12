<template>
  <q-layout view="hHh lpR fFf">
    <!-- HEADER -->
    <q-header elevated>
      <q-toolbar>
        <q-toolbar-title icon="dashboard">
          Calculadora de Subredes
        </q-toolbar-title>
        <q-space />

        <!-- Botones a la derecha -->
        <q-btn
          flat
          round
          dense
          label="SUB REDES BASICA"
          icon="dashboard"
          :color="activePanel === 'panel1' ? 'Green' : 'grey-10'"
          @click="activePanel = 'panel1'"
          class="q-mx-xs"
        />
        <q-btn
          flat
          round
          dense
          label="SUB REDES AVANZADA"
          icon="dashboard"
          :color="activePanel === 'panel2' ? 'Green' : 'grey-10'"
          @click="activePanel = 'panel2'"
          class="q-mx-xs"
        />
      </q-toolbar>
    </q-header>

    <!-- MAIN  -->
    <q-page-container>
      <q-page class="q-pa-md">
        <div v-if="activePanel === 'panel1'">
          <h6>Panel 1</h6>
          <p>Este es el contenido del primer panel.</p>
        </div>
        <div v-else-if="activePanel === 'panel2'">
          <h6>Panel 2</h6>
          <div class="container">
            <!-- Header -->
            <div class="header">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="icon"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M3 10h4l3 9 4-18 3 9h4"
                />
              </svg>
              <h1>Calculadora de Subredes Avanzado</h1>
              <p>
                Ingresa los parámetros de red para calcular las subredes
                automáticamente
              </p>
            </div>

            <!-- Formulario -->
            <div id="formCard" class="card">
              <div class="grid grid-cols-3">
                <div class="space-y-2">
                  <label for="ip">Dirección IP</label>
                  <input id="ip" type="text" placeholder="192.168.1.0" />
                </div>
                <div class="space-y-2">
                  <label for="mask">Máscara de Subred (CIDR)</label>
                  <input
                    id="mask"
                    type="number"
                    min="1"
                    max="30"
                    placeholder="24"
                  />
                </div>
                <div class="space-y-2">
                  <label for="subnets">Número de Subredes</label>
                  <input
                    id="subnets"
                    type="number"
                    min="1"
                    max="256"
                    placeholder="4"
                  />
                </div>
              </div>

              <!-- Hosts por Subred -->
              <div id="hostsContainer" class="grid hosts-grid mt-6"></div>

              <div style="display: flex; gap: 12px; margin-top: 24px">
                <button id="calculateBtn" class="btn primary" disabled>
                  Calcular Subredes
                </button>
                <button id="clearBtn" class="btn outline">Limpiar</button>
              </div>
            </div>

            <!-- Error -->
            <div id="errorAlert" class="error"></div>

            <!-- Resultados -->
            <div id="resultsCard" class="card" style="display: none">
              <h2
                style="
                  font-size: 1.25rem;
                  font-weight: 600;
                  margin-bottom: 16px;
                "
              >
                Resultados de Subredes
              </h2>
              <div class="table-container">
                <table>
                  <thead>
                    <tr>
                      <th class="center">Índice</th>
                      <th>Dirección de Red</th>
                      <th>Dirección del Router</th>
                      <th>Dirección Broadcast</th>
                      <th>Primer Host</th>
                      <th>Último Host</th>
                      <th class="center">Total Hosts</th>
                    </tr>
                  </thead>
                  <tbody id="resultsBody"></tbody>
                </table>
              </div>
            </div>

            <!-- Instrucciones -->
            <div class="card">
              <h3
                style="
                  font-size: 1.125rem;
                  font-weight: 600;
                  margin-bottom: 12px;
                "
              >
                Instrucciones de Uso
              </h3>
              <div
                style="
                  display: grid;
                  grid-template-columns: 1fr 1fr;
                  gap: 16px;
                  font-size: 0.875rem;
                  color: #475569;
                "
              >
                <div>
                  <h4 style="font-weight: 600; margin-bottom: 8px">
                    Campos de Entrada:
                  </h4>
                  <ul style="list-style: disc; padding-left: 20px">
                    <li>
                      <strong>Dirección IP:</strong> IP base de la red (ej:
                      192.168.1.0)
                    </li>
                    <li>
                      <strong>Máscara CIDR:</strong> Número de bits de la
                      máscara (ej: 24)
                    </li>
                    <li>
                      <strong>Número de Subredes:</strong> Cantidad de subredes
                      a crear
                    </li>
                    <li>
                      <strong>Hosts por Subred:</strong> Cantidad específica de
                      hosts para cada subred (VLSM)
                    </li>
                  </ul>
                </div>
                <div>
                  <h4 style="font-weight: 600; margin-bottom: 8px">
                    Información de la Tabla:
                  </h4>
                  <ul style="list-style: disc; padding-left: 20px">
                    <li>
                      <strong>Dirección de Red:</strong> Primera dirección de la
                      subred
                    </li>
                    <li>
                      <strong>Router:</strong> Generalmente la primera IP
                      utilizable
                    </li>
                    <li>
                      <strong>Broadcast:</strong> Última dirección de la subred
                    </li>
                    <li>
                      <strong>Primer/Último Host:</strong> Rango de IPs
                      asignables
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script>
export default {
  name: "PageTwo",
  data() {
    console.log("DArio puto cambio de pagina");
    return {
      activePanel: "panel1",
    };
  },
  mounted() {
    console.log("DArio puto: Componente montado");
    if (this.activePanel === "panel2") {
      this.initializePanel2Logic();
    }
  },
  watch: {
    activePanel(newValue, oldValue) {
      console.log(
        `DArio puto: activePanel cambió de ${oldValue} a ${newValue}`
      );
      if (newValue === "panel2") {
        this.$nextTick(() => {
          console.log(
            "DArio puto: activePanel es 'panel2', re-inicializando lógica del DOM"
          );
          this.initializePanel2Logic();
        });
      }
    },
  },
  methods: {
    initializePanel2Logic() {
      console.log("DArio puto: Ejecutando initializePanel2Logic");

      const ipInput = document.getElementById("ip");
      const maskInput = document.getElementById("mask");
      const subnetsInput = document.getElementById("subnets");
      const hostsContainer = document.getElementById("hostsContainer");
      const calculateBtn = document.getElementById("calculateBtn");
      const clearBtn = document.getElementById("clearBtn");
      const errorAlert = document.getElementById("errorAlert");
      const resultsCard = document.getElementById("resultsCard");
      const resultsBody = document.getElementById("resultsBody");
      if (
        !ipInput ||
        !maskInput ||
        !subnetsInput ||
        !hostsContainer ||
        !calculateBtn ||
        !clearBtn ||
        !errorAlert ||
        !resultsCard ||
        !resultsBody
      ) {
        console.error(
          "DArio puto: No se encontraron todos los elementos del DOM necesarios para Panel 2."
        );
        return;
      }

      let hostsPerSubnet = [];

      function ipToNumber(ip) {
        return (
          ip
            .split(".")
            .reduce((acc, octet) => (acc << 8) + parseInt(octet, 10), 0) >>> 0
        );
      }

      function numberToIp(num) {
        return [
          num >>> 24,
          (num >>> 16) & 255,
          (num >>> 8) & 255,
          num & 255,
        ].join(".");
      }

      function isValidIp(ip) {
        console.log("DArio puto ip valido");
        const parts = ip.split(".");
        return (
          parts.length === 4 &&
          parts.every((p) => {
            const n = parseInt(p, 10);
            return n >= 0 && n <= 255;
          })
        );
      }

      function updateHostsFields() {
        console.log("DArio puto: Entrando a updateHostsFields");
        const count = parseInt(subnetsInput.value, 10);
        console.log("DArio puto: Valor parseado de Número de Subredes:", count);

        hostsPerSubnet = [];
        hostsContainer.innerHTML = "";
        calculateBtn.disabled = true;
        console.log(
          "DArio puto: Botón deshabilitado al inicio de updateHostsFields"
        );

        if (count > 0 && count <= 256) {
          console.log(
            "DArio puto: Número de subredes válido. Creando campos de hosts."
          );
          for (let i = 0; i < count; i++) {
            hostsPerSubnet.push(0);
            const div = document.createElement("div");
            div.className = "space-y-2";
            div.innerHTML = `
                  <label for="hosts-${i}">Subred ${i + 1}</label>
                  <input id="hosts-${i}" type="number" min="1" placeholder="30" value="30" />
                `;
            hostsContainer.appendChild(div);
            const input = div.querySelector("input");
            hostsPerSubnet[i] = 30;
            input.addEventListener("input", (e) => {
              hostsPerSubnet[i] = parseInt(e.target.value, 10) || 0;
              console.log(
                `DArio puto: Valor de hosts para subred ${i + 1} cambiado a:`,
                hostsPerSubnet[i]
              );
              calculateBtn.disabled = hostsPerSubnet.some((h) => h < 1);
              console.log(
                "DArio puto: Estado del botón después de cambiar hosts:",
                calculateBtn.disabled ? "Deshabilitado" : "Habilitado"
              );
            });
          }
          calculateBtn.disabled = false;
          console.log(
            "DArio puto: Botón habilitado al final de updateHostsFields (número válido)"
          );
        } else {
          console.log(
            "DArio puto: Número de subredes inválido. Botón permanece deshabilitado."
          );
        }
      }

      subnetsInput.addEventListener("input", updateHostsFields);
      console.log("DArio puto: Listener añadido a subnetsInput");

      calculateBtn.addEventListener("click", () => {
        console.log("DArio puto calcular");
        errorAlert.textContent = "";
        resultsBody.innerHTML = "";
        resultsCard.style.display = "none";

        if (!isValidIp(ipInput.value)) {
          errorAlert.textContent = "Formato de IP inválido";
          return;
        }
        const maskNum = parseInt(maskInput.value, 10);
        const subnetsNum = parseInt(subnetsInput.value, 10);
        if (isNaN(maskNum) || maskNum < 1 || maskNum > 30) {
          errorAlert.textContent =
            "La máscara de subred debe estar entre 1 y 30";
          return;
        }
        if (isNaN(subnetsNum) || subnetsNum < 1 || subnetsNum > 256) {
          errorAlert.textContent =
            "El número de subredes debe estar entre 1 y 256";
          return;
        }
        if (
          hostsPerSubnet.length !== subnetsNum ||
          hostsPerSubnet.some((h) => h < 1)
        ) {
          errorAlert.textContent =
            "Debe especificar hosts válidos para todas las subredes";
          return;
        }

        const baseIp = ipToNumber(ipInput.value);
        const networkMask = (0xffffffff << (32 - maskNum)) >>> 0;
        let currentAddress = baseIp & networkMask;

        const requirements = hostsPerSubnet
          .map((hosts, idx) => {
            const bits = Math.ceil(Math.log2(hosts + 2));
            return { originalIndex: idx, hosts, subnetSize: Math.pow(2, bits) };
          })
          .sort((a, b) => b.subnetSize - a.subnetSize);

        const results = [];
        for (const req of requirements) {
          const netAddr = currentAddress;
          const broadcast = currentAddress + req.subnetSize - 1;
          const firstHost = netAddr + 1;
          const lastHost = broadcast - 1;
          results.push({
            index: req.originalIndex + 1,
            networkAddress: numberToIp(netAddr),
            routerAddress: numberToIp(firstHost),
            broadcastAddress: numberToIp(broadcast),
            firstHostAddress: numberToIp(firstHost),
            lastHostAddress: numberToIp(lastHost),
            totalHosts: req.subnetSize - 2,
            originalIndex: req.originalIndex,
          });
          currentAddress += req.subnetSize;
        }

        results.sort((a, b) => a.originalIndex - b.originalIndex);

        for (const r of results) {
          const row = document.createElement("tr");
          row.innerHTML = `
                <td class="center">${r.index}</td>
                <td class="font-mono">${r.networkAddress}</td>
                <td class="font-mono">${r.routerAddress}</td>
                <td class="font-mono">${r.broadcastAddress}</td>
                <td class="font-mono">${r.firstHostAddress}</td>
                <td class="font-mono">${r.lastHostAddress}</td>
                <td class="center">${r.totalHosts}</td>
              `;
          resultsBody.appendChild(row);
        }
        resultsCard.style.display = "block";
      });

      clearBtn.addEventListener("click", () => {
        console.log("DArio puto  llego aqui");
        ipInput.value = "";
        maskInput.value = "";
        subnetsInput.value = "";
        hostsContainer.innerHTML = "";
        hostsPerSubnet = [];
        calculateBtn.disabled = true;
        errorAlert.textContent = "";
        resultsBody.innerHTML = "";
        resultsCard.style.display = "none";
      });
    },
  },
};
</script>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
html,
body {
  height: 100%;
}
body {
  font-family: sans-serif;
  background: linear-gradient(to bottom right, #ebf8ff, #e0e7ff);
  color: #1e293b;
  line-height: 1.5;
}
.container {
  max-width: 1120px;
  margin: 0 auto;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 24px;
}
/* Encabezado */
.header {
  text-align: center;
}
.header .icon {
  vertical-align: middle;
  margin-right: 8px;
  width: 32px;
  height: 32px;
  stroke: #2563eb;
}
.header h1 {
  display: inline-block;
  font-size: 2rem;
  font-weight: bold;
  vertical-align: middle;
}
.header p {
  color: #475569;
  margin-top: 4px;
}
/* Tarjetas y diseños */
.card {
  background: white;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  padding: 24px;
}
.grid {
  display: grid;
  gap: 16px;
}
.grid-cols-3 {
  grid-template-columns: repeat(3, 1fr);
}
.hosts-grid {
  grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
}
.space-y-2 > * + * {
  margin-top: 8px;
}
/* Formulario */
label {
  display: block;
  font-size: 0.875rem;
  color: #334155;
}
input {
  width: 100%;
  padding: 8px;
  border: 1px solid #cbd5e1;
  border-radius: 4px;
  font-size: 1rem;
}
input:focus {
  outline: none;
  border-color: #6366f1;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.3);
}
/* Botones */
.btn {
  padding: 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 500;
  font-size: 1rem;
}
.btn.primary {
  background: #6366f1;
  color: white;
  flex: 1;
}
.btn.primary:disabled {
  background: #9ca3af;
  cursor: not-allowed;
}
.btn.outline {
  background: white;
  border: 1px solid #cbd5e1;
}
/* Alerta de error */
.error {
  color: #b91c1c;
  font-size: 0.875rem;
}
/* Tabla de resultados */
.table-container {
  overflow-x: auto;
}
table {
  width: 100%;
  border-collapse: collapse;
}
thead {
  background: #f8fafc;
}
th,
td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #e2e8f0;
  font-size: 0.875rem;
}
th.center,
td.center {
  text-align: center;
}
.font-mono {
  font-family: monospace;
}
</style>
