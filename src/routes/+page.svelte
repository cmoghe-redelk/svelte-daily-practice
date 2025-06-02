<script lang="ts">
	import { Rating } from '$lib';
	let rating: number = $state(2.5);
</script>

<h1>Welcome to SvelteKit</h1>
<Rating interactive={true} maxRating={5} starSize={'20px'} rating={rating} />
