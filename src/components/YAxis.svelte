<script>
	const { scale, x } = $props();

	const ticks = $derived(scale.ticks(scale.range()[0] / 100));
</script>

<g
	class="y-axis"
	transform="translate({x} 0)"
>
	{#each ticks as tick}
		<g
			class="tick"
			transform="translate(0 {scale(tick)})"
		>
			<text
				fill="black"
				font-size="10"
				text-anchor="end">{tick}</text
			>
		</g>
	{/each}
</g>

<style>
	g.y-axis {
		user-select: none;
	}
</style>
