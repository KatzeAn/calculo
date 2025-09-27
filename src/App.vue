<script lang="ts" setup>
import { ref } from "vue";
import { evaluate } from "mathjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  CategoryScale,
} from "chart.js";
import { Line } from "vue-chartjs";

// Registrar componentes de Chart.js
ChartJS.register(Title, Tooltip, Legend, LineElement, LinearScale, PointElement, CategoryScale);

// Variables reactivas
const functionInput = ref<string>("x^2 + 2x - 1");
const xValue = ref<number>(2);
const result = ref<string>("");

const chartData = ref<any>(generateChartData(functionInput.value));

// --- Función para calcular el límite ---
function calculateLimit() {
  try {
    const h = 0.0001;
    const left = evaluate(functionInput.value, { x: xValue.value - h });
    const right = evaluate(functionInput.value, { x: xValue.value + h });

    const limit = (left + right) / 2;
result.value = `El límite aproximado de f(x) en x=${xValue.value} es: ${limit}`;
  } catch {
    result.value = "⚠️ La función no es válida. Revisa la sintaxis.";
  }

  // Actualizar la gráfica después de calcular
  chartData.value = generateChartData(functionInput.value);
}

// --- Generar datos para la gráfica ---
function generateChartData(expr: string) {
  const xs: number[] = [];
  const ys: number[] = [];

  for (let x = -10; x <= 10; x += 0.5) {
    try {
      xs.push(x);
      ys.push(evaluate(expr, { x }));
    } catch {
      xs.push(x);
      ys.push(NaN);
    }
  }

  return {
    labels: xs,
    datasets: [
      {
        label: `f(x) = ${expr}`,
        data: ys,
        borderColor: "rgb(42, 62, 244)",
        backgroundColor: "rgba(42, 62, 244, 0.3)",
        tension: 0.3,
      },
    ],
  };
}

// Opciones del gráfico
const chartOptions = {
  responsive: true,
  plugins: {
    legend: { position: "top" as const },
    title: { display: true, text: "Gráfica de f(x)" },
  },
};
</script>

<template>
 <div class="p-4 sm:p-6 max-w-full sm:max-w-2xl mx-auto bg-white shadow-lg rounded-2xl">
  <h1 class="text-lg sm:text-xl font-bold mb-4 text-gray-800 !text-gray-800 text-center sm:text-left">
  Calculadora de Límites y Gráfica
</h1>


  <!-- Inputs -->
  <div class="flex flex-col sm:flex-row sm:items-center gap-4 mb-6">
    <div class="flex-1">
      <label class="block mb-1 font-medium text-gray-700">Función f(x):</label>
      <input
        v-model="functionInput"
        type="text"
        class="w-full border border-gray-300 p-2 rounded-md focus:ring-2 focus:ring-blue-400 focus:outline-none"
        placeholder="Ejemplo: x^2 + 2x - 1"
      />
    </div>

    <div class="flex-1">
      <label class="block mb-1 font-medium text-gray-700">Valor de x:</label>
      <input
        v-model="xValue"
        type="number"
        class="w-full border border-gray-300 p-2 rounded-md focus:ring-2 focus:ring-blue-400 focus:outline-none"
      />
    </div>
  </div>

  <!-- Botones -->
  <div class="flex flex-col sm:flex-row gap-4 mb-4">
    <button
      @click="calculateLimit"
      class="w-full sm:w-auto bg-blue-500 text-white px-6 py-3 rounded-xl shadow-md hover:bg-blue-600 hover:shadow-lg transition duration-300"
    >
      Calcular límite y graficar
    </button>

    <button
      @click="() => { functionInput = ''; xValue = null; result = ''; }"
      class="w-full sm:w-auto bg-gray-200 text-gray-800 px-6 py-3 rounded-xl shadow-md hover:bg-gray-300 hover:shadow-lg transition duration-300"
    >
      Limpiar
    </button>
  </div>

  <!-- Resultado -->
  <p class="mt-2 font-medium text-gray-800 text-center sm:text-left">{{ result }}</p>

  <!-- Gráfica -->
  <div class="mt-6 w-full">
    <Line :data="chartData" :options="chartOptions" />
  </div>
</div>
</template>

<style>


body {
  background: white !important;
  font-family: sans-serif;
  color: #000000 !important; /* gris oscuro */
}

/* Fuerza el color de los textos principales */
h1, h2, h3, h4, h5, h6, p, label {
  color: #1f2937 !important; /* gris oscuro */
}

</style>
