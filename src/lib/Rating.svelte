<script lang="ts">
	import Star from './Star.svelte';
	interface Props {
		maxRating?: number;
		starSize?: string;
		rating: number;
		interactive?: boolean;
	}
	let {
		maxRating = 5,
		starSize = '20px',
		rating = $bindable(),
		interactive = false
	}: Props = $props();
	let ratingValues = $state(new Array(maxRating).fill(0));
	$effect(() => {
		let fillWholeUntil = Math.floor(rating / 1) - 1;
		for (let i = 0; i < ratingValues.length; i++) {
			if (i <= fillWholeUntil) {
				ratingValues[i] = 100;
			} else if (i === fillWholeUntil + 1) {
				ratingValues[i] = (rating % 1) * 100;
			} else {
				ratingValues[i] = 0;
			}
		}
	});

	let hoveredIdx = $state(0);
	let isHovering = $state(false);
	let lastStarHoverPercent = $state(0);
	let hoverRating = $state(0);

	$effect(() => {
		console.log({ hoveredIdx, isHovering });
	});

	const onMouseOver = (ev: MouseEvent) => {
		const target = ev.target as SVGPolygonElement;
		if (target) {
			isHovering = true;
		}
		if (target?.parentElement?.id) {
			hoveredIdx = Number(target?.parentElement.id);
		}
	};
	const onMouseLeave = () => {
		isHovering = false;
	};
	const onMouseMove = (ev: MouseEvent) => {
		const target = ev.target as SVGPolygonElement;
		if (target?.parentElement?.id) {
			hoveredIdx = Number(target.parentElement.id);
			const starBoundingBox = target.parentElement.getBoundingClientRect();
			const hoveredPosition = ev.clientX - starBoundingBox.left;
			const percentCovered = Math.max(
				0,
				Math.min(100, (hoveredPosition / starBoundingBox.width) * 100)
			);
			lastStarHoverPercent = percentCovered;
			hoverRating = hoveredIdx + Number((percentCovered * 0.01).toFixed(1));
		}
	};
	const onFocus = (ev: FocusEvent) => {
		const target = ev.target as SVGPolygonElement;
		if (target?.parentElement?.id) {
			hoveredIdx = Number(target?.parentElement.id);
		}
	};

	const onClick = (ev: MouseEvent) => {
		const target = ev.target as SVGPolygonElement;
		if (target?.parentElement?.id) {
			rating = Number(target.parentElement.id) + Number((lastStarHoverPercent * 0.01).toFixed(1));
		}
	};

	let ratingProps = $state({});

	ratingProps = interactive
		? {
				onmouseleave: onMouseLeave,
				onmousemove: onMouseMove,
				onmouseover: onMouseOver,
				onfocus: onFocus,
				onclick: onClick,
				role: 'list'
			}
		: {};

	const getHoveredPercent = (idx: number) => {
		if (hoveredIdx > idx) {
			return 100;
		} else if (hoveredIdx < idx) {
			return 0;
		} else {
			return lastStarHoverPercent;
		}
	};
</script>

<div class="container" {...ratingProps}>
	{#each ratingValues as ratingPercent, idx}
		<Star
			size={starSize}
			percent={ratingPercent}
			id={'' + idx}
			{interactive}
			isHovered={isHovering}
			hoveredPercent={getHoveredPercent(idx)}
		/>
	{/each}
	<span class="rating-text">
		{#if isHovering}
			{hoverRating}
		{:else}
			{rating}
		{/if}
		/ {maxRating}
	</span>
</div>

<style>
	.container {
		display: inline-block;
	}

	.rating-text {
		margin-inline-start:20px;
		font-size:20px;
		font-weight: 500;
		font-family: '0xProto Nerd Font';
	}

</style>
