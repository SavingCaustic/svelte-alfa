<script>
	export let label;
	let isOpen = false;
</script>

<div class="panel">
	<button class="panel-header" on:click={() => isOpen = !isOpen}>
		{label} {isOpen ? "▲" : "▼"}
	</button>
	{#if isOpen}
		<div class="panel-content">
			<slot />
		</div>
	{/if}
</div>

<style>
  .panel {
    border: 1px solid #ccc;
    border-radius: 4px;
    margin-bottom: 8px;
    overflow: hidden;
  }

  .panel-header {
    width: 100%;
    text-align: left;
    background: #f4f4f4;
    border: none;
    padding: 10px;
    font-size: 1.1em;
    cursor: pointer;
  }

  .panel-content {
    padding: 10px;
    background: white;
  }
</style>
