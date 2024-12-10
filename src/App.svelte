<script>
	import Timer from './lib/components/Timer.svelte';
</script>

<main>
	<h1>Custom Timers</h1>
	<Timer name="Workout Timer" duration={600} />
	<Timer name="Study Timer" duration={1500} />
</main>

<style>
    main {
        font-family: Arial, sans-serif;
        padding: 2rem;
        text-align: center;
    }
</style>
