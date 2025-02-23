<script lang="ts">
	import '../app.css';
	import NavBar from '../lib/components/NavBar.svelte';
	let { children } = $props();
</script>

<div class="flex h-screen w-full flex-col items-center pt-16">
	<NavBar />
	{@render children()}
</div>
