<script>
	export let line = "";
	let isFocused = false;
	let edited = false;
	let input;
	
	$: if(isFocused === true && input) input.focus(); 
</script>	

{#if isFocused}
	<input bind:this={input} bind:value={line} type="text" 
				on:input="{() => edited = true}" 
				size={line.length}	
				on:keyup="{(e) => {if (e.keyCode === 13) isFocused = false;}}"
	/>
{:else}
	<p on:click={() => isFocused = true} class:edited>
		{line}
	</p>
{/if}

<style>
	input {
		display: block;
		color: black;
		margin-top: 0;
	}
	p {
		display: block;
		color: black;
		margin-top: 0;
	}
	.edited {
		font-weight: 600;
	}
</style>