<script>
	import { scaleTime, scaleLinear, extent, max, line } from 'd3';
	import { fade } from 'svelte/transition';

	import XAxis from './XAxis.svelte';
	import YAxis from './YAxis.svelte';

	const { data, breakDate } = $props();

  // padding around the chart
	const padding = 24;

  // flag if drawing mode is on
	let isDrawing = false;

  // reactive variable declarations
	let width = $state();
	let height = $state();
	let drawnLine = $state([]);
	let showResult = $state(false);

  // a function to generate the line from points
	const lineGenerator = line()
		.x((d) => d.x)
		.y((d) => d.y);

  // the scale function to transform dates into pixels
	const dateScale = $derived(
		scaleTime()
			.domain(extent(data, (d) => d.date))
			.range([padding, width - padding])
	);

  // the scale function to transform prices into pixels
	const priceScale = $derived(
		scaleLinear()
			.domain([0, max(data, (d) => d.price)])
			.range([height - padding, padding])
	);

  // the data to be rendered, with x and y positions for the line
	const renderedData = $derived(
		data.map((d) => ({
			...d,
			x: dateScale(d.date),
			y: priceScale(d.price)
		}))
	);

  // three functions that handle mouse dow, mouse move and mouse up on the chart
	function handleMouseDown() {
		isDrawing = true;
		drawnLine = [];
	}

	function handleMouseMove(event) {
		if (!isDrawing) return;

    // get the current mouse position
		const { offsetX: x, offsetY: y } = event;

    // and store it in the drawnLine array
		drawnLine.push({ x, y });
	}

	function handleMouseUp() {
		isDrawing = false;
		showResult = true;
	}
</script>

<div
	class="chart"
	bind:clientWidth={width}
	bind:clientHeight={height}
>
	{#if width && height && data.length}
		<svg
			width={width}
			height={height}
			onmousedown={handleMouseDown}
			onmousemove={handleMouseMove}
			onmouseup={handleMouseUp}
		>
			<path
				class="data-line"
				d={lineGenerator(renderedData)}
			/>
			{#if !showResult}
				<rect
					class="hide-rectangle"
					x={dateScale(breakDate)}
					y="0"
					width={width - dateScale(breakDate)}
					height={height}
					out:fade
				/>
			{/if}
			<line
				class="break-date"
				x1={dateScale(breakDate)}
				y1="0"
				x2={dateScale(breakDate)}
				y2={height - padding}
			/>
			<path
				class="drawn-line"
				d={lineGenerator(drawnLine)}
			/>
			<XAxis
				scale={dateScale}
				y={height - padding}
			/>
      <YAxis
				scale={priceScale}
				x={padding}
			/>
		</svg>
	{/if}
</div>

<style>
	.chart {
		width: 100%;
		height: 100%;
    cursor: crosshair;
	}

	path.data-line {
		fill: none;
		stroke: purple;
		stroke-width: 4;
		stroke-linecap: round;
	}

	rect.hide-rectangle {
		fill: white;
		pointer-events: none;
	}

	line.break-date {
		stroke: gray;
		stroke-width: 2;
		stroke-dasharray: 2 2;
	}

	path.drawn-line {
		fill: none;
		stroke: black;
		stroke-width: 2;
		stroke-linecap: round;
	}
</style>
