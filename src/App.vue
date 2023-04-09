<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'
const heights = ref([])
const heightChartEl = ref(null)
const heightChart = shallowRef(null)
const heightInput = ref(0)
const currentHeight = computed(() => {
	return heights.value.sort((a, b) => b.date - a.date)[0] || { height: 0 }
})
const addHeight = () => {
	heights.value.push({
		height: heightInput.value,
		date: new Date().getTime()
	})
}
watch(heights, (newHeights) => {
	const ws = [...newHeights]
	if (heightChart.value) {
		heightChart.value.data.labels = ws
			.sort((a, b) => a.date - b.date)
			.map(height => new Date(height.date).toLocaleDateString() )
			.slice(-36)
		heightChart.value.data.datasets[0].data = ws
			.sort((a, b) => a.date - b.date)
			.map(height => height.height)
			.slice(-36)
		heightChart.value.update()
		return
	}
	nextTick(() => {
		heightChart.value = new Chart(heightChartEl.value.getContext('2d'), {
			type: 'line',
			data: {
				labels: ws
					.sort((a, b) => a.date - b.date)
					.map(height => new Date(height.date).toLocaleDateString()),
				datasets: [
					{
						label: 'Height',
						data: ws
							.sort((a, b) => a.date - b.date)
							.map(height => height.height),
						backgroundColor: 'rgba(25, 105, 180, 0.2)',
						borderColor: 'rgba(5, 105, 180, 1)',
						borderWidth: 1,
						fill: true
					}
				]
			},
			options: {
				responsive: true,
				maintainAspectRatio: false
			}
		})
	})
}, { deep: true })
</script>

<template>
	<main>

		<h1>Height Tracker</h1>

		<div class="current">
			<span>{{ currentHeight.height }}</span>
			<small>Current Height (cm)</small>
		</div>

		<form @submit.prevent="addHeight">
			<input
				type="number"
				step="0.1"
				v-model="heightInput" />

			<input
				type="submit"
				value="Add Height" />
		</form>

		<div v-if="heights && heights.length > 0">

			<h2>
				Last 36 Measurment
			</h2>

			<div class="canvas-box">
				<canvas ref="heightChartEl"></canvas>
			</div>

			<div class="height-history">
				<h2>Height History</h2>
				<ul>
					<li v-for="height in heights" :key="height.id">
						<span>{{ height.height }}cm
              </span>
						<small>
							{{ new Date(height.date).toLocaleDateString() }}
						</small>
					</li>
				</ul>
			</div>

		</div>

	</main>
</template>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'montserrat', sans-serif;
}
body {
	background-color:rgb(193, 255, 231)
}
main {
	padding: 1.5rem;
}
h1 {
	font-size: 2em;
	color: rgb(119, 192, 255);
	text-align: center;
	margin-bottom: 2rem;
}
h2 {
	margin-bottom: 1rem;
	color: #007bc7;
	font-weight: 400;
}
.current {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	width: 200px;
	height: 200px;
color: #007bc7;
	text-align: center;
	background-color: rgb(239, 239, 239);
	border-radius: 9px;
	box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.945);
	border: 1px solid hwb(330 15% 83%);
	margin: 0 auto 2rem;
}
.current span {
	display: block;
	font-size: 2em;
	font-weight: bold;
	margin-bottom: 0.5rem;
}
.current small {
	color: #888;
	font-style: italic;
}
form {
	display: flex;
	margin-bottom: 2rem;
	border: 1px solid #AAA;
	border-radius: 0.5rem;
	overflow: hidden;
	transition: 200ms linear;
}
form:focus-within,
form:hover {
	border-color: rgb(130, 117, 123);
	border-width: 2px;
}
form input[type="number"] {
	appearance: none;
	outline: none;
	border: none;
  color: #0e0e0e;
	background-color: rgb(239, 239, 239);
	flex: 1 1 0%;
	padding: 1rem 1.5rem;
	font-size: 1.25rem;
}
form input[type="submit"] {
	appearance: none;
	outline: none;
	border: none;
	cursor: pointer;
	background-color: rgb(158, 133, 145);
	padding: 0.5rem 1rem;
	color: rgb(48, 38, 38);
	font-size: 1.25rem;
	font-weight: 700;
	transition: 200ms linear;
	border-left: 3px solid transparent;
}
form input[type="submit"]:hover {
	background-color: rgb(190, 168, 187);
	color: rgb(147, 130, 138);
	border-left-color: rgb(135, 115, 125);
}
.canvas-box {
	width: 100%;
	max-width: 720px;
	background-color: rgb(255, 248, 248);
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 10px 10px 10px rgb(62, 60, 60);
	margin-bottom: 2rem;
}
.height-history ul {
	list-style: none;
	padding: 0;
	margin: 0;
}
.height-history ul li {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 0.5rem;
	cursor: pointer;
	background-color: #97fbf4;
}
.height-history ul li:nth-child(even) {
	background-color: #c9abab;
}
.height-history ul li:hover {
	background-color: #388e88;
}
.height-history ul li:last-of-type {
	border-bottom: none;
}
.height-history ul li span {
	display: block;
	font-size: 1.25rem;
	font-weight: 700;
	margin-right: 1rem;
}
.height-history ul li small {
	color: #0e0e0e;
	font-style: italic;
}
</style>
