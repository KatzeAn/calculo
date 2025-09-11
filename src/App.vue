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
  <div class="p-6 max-w-2xl mx-auto bg-white shadow rounded-2xl">
    <h1 class="text-xl font-bold mb-4">Calculadora de Límites y Gráfica</h1>

    <label class="block mb-2">Función f(x):</label>
    <input
      v-model="functionInput"
      type="text"
      class="w-full border p-2 rounded mb-4"
      placeholder="Ejemplo: x^2 + 2x - 1"
    />

    <label class="block mb-2">Valor de x:</label>
    <input
      v-model="xValue"
      type="number"
      class="w-full border p-2 rounded mb-4"
    />

    <button
      @click="calculateLimit"
      class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
    >
      Calcular límite y graficar
    </button>

    <p class="mt-4 font-medium">{{ result }}</p>

    <!-- Gráfica -->
    <div class="mt-6">
      <Line :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<style>
body {
  background: black;
  font-family: sans-serif;
}
</style>