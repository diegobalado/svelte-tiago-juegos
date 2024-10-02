<script>
	import { onMount } from 'svelte';
	import Background from '../../components/Background.svelte';
	import { random } from 'node-emoji';
	import { writable } from 'svelte/store';
	import Button from '../../components/Button.svelte';

	let randomEmoji = writable();
	let respondido = writable(false);
	let correcto = writable(false);
	let successLabel = writable('');
	let contador = writable(0);

	onMount(() => {
		$randomEmoji = random();
	});

	
	const columnas = 6;
	const filas = 8;
	const cantidad = filas * columnas;
	const emojiDistinto = Math.floor(Math.random() * cantidad);
	const getEmojiClasses = (i = 0) => `emoji ${i === emojiDistinto ? 'distinto' : ''}`;
	const handleClick = (i = 0) => {
		$respondido = true;
		$correcto = i === emojiDistinto;
		$successLabel = $correcto ? 'BIEEN!! 🎉🎉' : 'NOOO!! 💩💩💩';
		if ($correcto) {
			$contador++;
		} else {
			$contador = 0;
		}
	};
	const handleNew = () => {
		$randomEmoji = random();
		$respondido = false;
	};
</script>

<svelte:head>
	<title>Emojis</title>
	<meta name="description" content="Emojis" />
</svelte:head>

<div class="overflow-hidden absolute w-full h-full bg-gradient-to-r from-slate-900 to-slate-700">
	<Background />
	<div class="flex relative z-10 flex-col justify-center items-center w-full h-full">
		<div class="absolute top-6 right-6">
			<p class="text-2xl font-extrabold text-slate-300">{`⭐ ${$contador}`}</p>
		</div>

		<p class="font-sans text-3xl font-bold text-center uppercase text-slate-300 text-w max-w-72">Elegí el emoji distinto</p>
		<div class="grid grid-cols-6 gap-2 place-items-center px-4 py-3 my-5 rounded-xl border-4 border-gray-500 opacity-90 bg-slate-200 md:grid-cols-8 md:gap-8">
			{#if $randomEmoji}
				{#each Array.from({ length: cantidad }) as _, i}
					<button on:click={() => handleClick(i)}>
						<p class={getEmojiClasses(i)}>
							{$randomEmoji.emoji}
						</p>
					</button>
				{/each}
			{:else}
				<p class="col-span-6 row-span-3 text-2xl text-white">Cargando...</p>
			{/if}
		</div>
		{#if $respondido}
			<div
				class="flex absolute justify-center items-center w-full h-full before:absolute before:w-full before:h-full before:backdrop-blur-sm"
			>
				<Button label={$successLabel} onClick={() => handleNew()} />
			</div>
		{/if}
	</div>
</div>
