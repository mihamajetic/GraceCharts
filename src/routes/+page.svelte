<script lang="ts">
	import { Line } from 'svelte-chartjs';
	import { Pie } from 'svelte-chartjs';
	import { data } from '$lib/data.js';

	import {
		Chart as ChartJS,
		Title,
		Tooltip,
		Legend,
		LineElement,
		LinearScale,
		PointElement,
		ArcElement,
		CategoryScale,
		elements
	} from 'chart.js';

	let year: number = 2023;
	let afterYear: number = Math.floor(Math.random() * (1953 - 2023) + 2023);
	ChartJS.register(
		Title,
		Tooltip,
		Legend,
		LineElement,
		LinearScale,
		ArcElement,
		PointElement,
		CategoryScale
	);
	type ChartData = {
		labels: (string | number)[];
		datasets: {
			label: string;
			fill: boolean;
			borderColor: string;
			data: number[];
			backgroundColor?: string[];
			hoverBackgroundColor?: string[];
		}[];
	};

	let chart1: ChartData = {
		labels: data.map((entry) => entry.year), // Use actual years from dt
		datasets: [
			{
				label: 'Male',
				fill: false,
				borderColor: 'rgb(54, 162, 235)',
				data: data.map((entry) => entry.m)
			},
			{
				label: 'Female',
				fill: false,
				borderColor: 'rgb(255, 99, 132)',
				data: data.map((entry) => entry.f)
			}
		]
	};

	let chart2 = {
		labels: ['Male', 'Female'],
		datasets: [
			{
				data: getDataByYear(year),
				backgroundColor: ['#F7464A', '#46BFBD'],
				hoverBackgroundColor: ['#FF5A5E', '#5AD3D1']
			}
		]
	};

	$: {
		chart2.datasets[0].data = getDataByYear(year);
	}

	// Fixed function to get data by year
	function getDataByYear(year: number) {
		const output = data.find((element) => element.year === year);
		return [output?.m, output?.f];
	}

	function getDataByGender(gender: 'm' | 'f') {
		const output = data.map((element) => ({
			year: element.year,
			value: gender === 'm' ? element.m : element.f
		}));
		return output;
	}
	console.log(getDataByGender('m'));

	function maxTotalDiff() {
		let maxDiff = [undefined, 0];

		data.forEach((element) => {
			const diff = Math.abs(element.m - element.f);
			if (diff > maxDiff[1]) {
				maxDiff = [element.year, diff];
			}
		});

		return maxDiff;
	}

	function maxPercentageDiff() {
		let maxDiff = [undefined, 0];
		data.forEach((element) => {
			const total = element.m + element.f;
			const diff = (Math.abs(element.m / total - element.f / total) * 100).toFixed(2);
			if (diff > maxDiff[1]) {
				maxDiff = [element.year, diff];
			}
		});
		return maxDiff;
	}

	function maxTotal(year: number) {
		let max = [undefined, 0];
		data.forEach((element) => {
			const total = element.m + element.f;
			if (element.year >= year) {
				if (total > max[1]) {
					max = [element.year, total];
				}
			}
		});
		return max;
	}
	$: {
		maxTotal(afterYear);
	}
</script>

<div class="container mx-auto">
	<div class="grid grid-cols-2 gap-10 mb-10">
		<div class="border-4 border-violet-200 p-3">
			<h2 class="text-center text-2xl text-violet-700 font-bold">
				Graf podatkov o rojstnih za obdobje 1954 - 2023
			</h2>
			<Line data={chart1} options={{ responsive: true }} />
		</div>
		<div class="border-4 border-violet-200 p-3">
			<h2 class="text-center text-2xl text-violet-700 font-bold">
				Razdelitev rojstev po posameznem letu
			</h2>
			<input
				class="border border-2 w-full text-center p-2 border-violet-200"
				bind:value={year}
				type="number"
				min="1954"
				max="2023"
			/>
			<Pie data={chart2} options={{ responsive: true }} />
		</div>
	</div>
	<div class="grid grid-cols-3 gap-10">
		<div class="border-4 border-violet-200 p-3">
			<h1 class="text-2xl">Največje skupno število rojstev po letu</h1>
			<input
				type="number"
				bind:value={afterYear}
				class="border border-2 w-full text-center p-2 border-violet-200"
				min="1953"
				max="2023"
			/>
			<p>{maxTotal(afterYear)[1]} v letu {maxTotal(afterYear)[0]}</p>
		</div>
		<div class="border-4 border-violet-200 p-3">
			<h1 class="text-2xl">Največja razlika</h1>
			<p>{maxTotalDiff()[1]} v letu {maxTotalDiff()[0]}</p>
		</div>
		<div class="border-4 border-violet-200 p-3">
			<h1 class="text-2xl">Največja razlika v %</h1>
			<p>{maxPercentageDiff()[1]}% v letu {maxPercentageDiff()[0]}</p>
		</div>
	</div>
</div>
