<script>
	import { onMount } from 'svelte';
	import Background from '../../components/Background.svelte';
	import { random } from 'node-emoji';
	import { writable } from 'svelte/store';

	/**
	 * @type {{
	 *   name: string,
	 *   emoji: string
	 * } | null }
	 */

	let randomEmoji;

	let respondido = writable(false);
	let correcto = writable(false);

	onMount(() => {
		randomEmoji = random();
	});

	const columnas = 6;
	const filas = 8;
	const cantidad = filas * columnas;
	const emojiDistinto = Math.floor(Math.random() * cantidad);
	const getEmojiClasses = (i = 0) => `emoji ${i === emojiDistinto ? 'distinto' : ''}`;
	const handleClick = (i = 0) => {
		$respondido = true;
		$correcto = i === emojiDistinto;
	};
	const handleReload = () => {
		location.reload();
	};
</script>

<svelte:head>
	<title>Emojis</title>
	<meta name="description" content="Emojis" />
</svelte:head>

<div class="absolute h-full w-full overflow-hidden bg-gradient-to-r from-slate-900 to-slate-700">
	<Background />
	<div class="flex flex-col w-full h-full items-center justify-center z-10 relative">
		<div class="grid grid-cols-6 md:grid-cols-8 gap-2 md:gap-8">
			{#if randomEmoji}
				{#each Array.from({ length: cantidad }) as _, i}
					<button on:click={() => handleClick(i)}>
						<p class={getEmojiClasses(i)}>
							{randomEmoji.emoji}
						</p>
					</button>
				{/each}
			{:else}
				<p class="text-white text-2xl">Cargando...</p>
			{/if}
		</div>
		{#if $respondido}
			<button on:click={() => handleReload()} class="btn btn-active btn-accent">
				{#if $correcto}
					<p>Correcto!! 🎉🎉</p>
				{:else}
					<p>NOOO!! 💩💩💩</p>
				{/if}
			</button>
		{/if}
	</div>
</div>
