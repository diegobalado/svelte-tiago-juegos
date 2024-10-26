<script>
	export let label;
	export let onClick;
</script>

<button 
	class="group relative h-12 w-auto px-4 overflow-hidden rounded-lg bg-white text-lg shadow"
	on:click={onClick}
>
	<div
		class="absolute inset-0 w-3 bg-green-600 transition-all duration-[250ms] ease-out group-hover:w-full"
	></div>
	<span class="relative text-black group-hover:text-white pl-3">
		{label}
	</span>
</button>
