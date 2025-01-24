<script>
	import { timeFormat } from 'd3';

	const { scale, y } = $props();

	const formatDate = timeFormat('%Y');

	const ticks = $derived(scale.ticks(scale.range()[1] / 100));
</script>

<g
	class="x-axis"
	transform="translate(0 {y})"
>
	<line
		x1={scale.range()[0]}
		x2={scale.range()[1]}
		y1="0"
		y2="0"
		stroke="black"
	/>
	<g class="ticks">
		{#each ticks as tick}
			<g
				class="tick"
				transform={`translate(${scale(tick)} 0)`}
			>
				<line
					x1="0"
					x2="0"
					y1="0"
					y2="6"
					stroke="black"
				/>
				<text
					x="0"
					y="9"
					dy="0.71em"
					fill="black"
					font-size="10"
					text-anchor="middle"
				>
					{formatDate(tick)}
				</text>
			</g>
		{/each}
	</g>
</g>

<style>
  g.x-axis {
    user-select: none;
  }
</style>
