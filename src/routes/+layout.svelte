<script>
	import 'bootstrap/dist/css/bootstrap.css';
	import scriptSrc from 'bootstrap/dist/js/bootstrap.bundle.js?url';
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

<slot></slot>
