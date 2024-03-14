<script lang="ts">
	export let click = (e: MouseEvent) => {
		e.preventDefault();
		alert('teste');
	};
</script>

<button on:click={click}>
	<slot />
</button>
