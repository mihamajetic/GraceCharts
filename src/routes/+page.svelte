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
		CategoryScale
	} from 'chart.js';

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
		labels: ['A', 'B', 'C', 'D'],
		datasets: [
			{
				label: 'Linija 1',
				fill: false,
				borderColor: 'rgb(54, 162, 235)',
				data: [14, 1, 3, 15]
			},
			{
				label: 'Linija 2',
				fill: false,
				borderColor: 'rgb(255, 99, 132)',
				data: [12, 9, 30, 1]
			}
		]
	};
</script>

<div class="container mx-auto">
	<div class="grid grid-cols-2 gap-10 mb-10">
		<div class="border-4 border-violet-200 p-3">
			<h2 class="text-center text-2xl text-violet-700 font-bold">Graf podatkov</h2>
			<Line data={chart1} options={{ responsive: true }} />
		</div>
	</div>
</div>
