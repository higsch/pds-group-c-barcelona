<script>
	import { csv, timeParse } from 'd3';

	import Chart from './components/Chart.svelte';

	// function to parse date
	const parseDate = timeParse('%Y-%m-%d');

	// load the data
	let data = $state([]);
	csv('data/data.csv').then((d) => {
		data = d.map((d) => ({
			date: parseDate(d.date),
			price: +d.price
		}));
	});
</script>

<main>
  <h2>Wie haben sich die Mietpreise in Barcelona nach der Maßnahme zur Mietpreisregulierung entwickelt?</h2>
  <h4>Schätzen Sie und zeichnen Sie die Linie weiter!</h4>
	<div class="chart-container">
		<Chart
			data={data}
			breakDate={parseDate('2021-01-01')}
		/>
	</div>
</main>

<style>
	main {
		width: 100%;
		height: 400px;
	}

  h2 {
    margin: 8px 0;
    font-family: Georgia, serif;
    font-size: 16px;
  }

  h4 {
    font-family: Georgia, serif;
    font-size: 14px;
    font-weight: normal;
  }

  .chart-container {
    width: 100%;
    height: 100%;
  }
</style>
