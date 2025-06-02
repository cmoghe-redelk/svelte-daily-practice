<script lang="ts">
	interface Props {
		size: string;
		ratingColor?: string;
		percent: number;
		id: string;
		isHovered?:boolean;
		interactive?: boolean;
		hoveredPercent?:number;
	}

	let {
		size = '20px',
		ratingColor = 'gold',
		percent = 0,
		id = '',
		interactive = false,
		isHovered = false,
		hoveredPercent,
	}: Props = $props();

</script>

<svg
	id={id}
	viewBox="0 -0.5 33 33"
	version="1.1"
	xmlns="http://www.w3.org/2000/svg"
	xmlns:xlink="http://www.w3.org/1999/xlink"
	fill="#000000"
	width={size}
	height={size}
	class:filled={percent === 100 && interactive}
	class:interactive
>
	<defs>
		<linearGradient id={'rating-gradient' + id} x1="0%" x2="100%" y1="0%" y2="0%">
			<stop offset={`${isHovered ? hoveredPercent: percent}%`} stop-color={ratingColor}></stop>
			<stop offset={`${isHovered  ? hoveredPercent: percent}%`} stop-color="black"></stop>
		</linearGradient>
	</defs>
	<polygon
		fill={`url(#rating-gradient${id})`}
		points="27.865 31.83 17.615 26.209 7.462 32.009 9.553 20.362 0.99 12.335 12.532 10.758 17.394 0 22.436 10.672 34 12.047 25.574 20.22"
	>
	</polygon>
</svg>

<style>
	.interactive {
		cursor: pointer;
	}
	svg:hover {
		transform: scale(1.29);
		transition: transform 100ms ease-in;
	}
</style>
