<script lang="ts">
  import '../app.css';
  let { children } = $props();
</script>

<svelte:head><title>grace.</title></svelte:head>

<main>{@render children()}</main>

<style>
  main { min-height: 100vh; min-height: 100svh; display: grid; place-items: center; padding: 32px 24px; }
</style>
