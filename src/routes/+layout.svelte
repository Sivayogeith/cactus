<script lang="ts">
	import 'bootstrap/dist/css/bootstrap.css';
	import scriptSrc from 'bootstrap/dist/js/bootstrap.bundle.js?url';
	interface Props {
		children?: import('svelte').Snippet;
	}

	let { children }: Props = $props();
</script>

<style>
	:root {
		--bs-primary: #198754;
		--bs-primary-rgb: 25, 135, 84;
	}
</style>

<svelte:head>
	<script src={scriptSrc}></script>
</svelte:head>

{@render children?.()}
