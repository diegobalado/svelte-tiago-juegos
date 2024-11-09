<script>
	import { onMount, onDestroy } from 'svelte';
	import Background from '../../components/Background.svelte';
	import { random } from 'node-emoji';
	import { writable } from 'svelte/store';
	import Button from '../../components/Button.svelte';
	import Timer from '../../components/Timer.svelte';

	const duration = 10;
	let randomEmoji = writable();
	let respondido = writable(false);
	let correcto = writable(false);
	let successLabel = writable('');
	let contador = writable(0);
	let emojiDistinto = writable(0);
	let timer = writable(duration);
	let blur = writable(false);
	let timerEnabled = writable(true);
	/** @type {ReturnType<typeof setInterval> | null} */
	let interval = null;

	onMount(() => {
		$randomEmoji = random();
		$emojiDistinto = getRandomIndex();
		if ($timerEnabled) {
			startTimer();
		}
	});

	const columnas = 6;
	const filas = 6;
	const cantidad = filas * columnas;
	const getRandomIndex = () => Math.floor(Math.random() * cantidad);
	const handleClick = (i = 0) => {
		$respondido = true;
		$blur = true;
		$correcto = i === $emojiDistinto;
		$successLabel = $correcto ? '¡¡BIEEN!! 🎉🎉🎉' : '¡¡NOOO!! 💩💩💩';
		if ($correcto) {
			$contador++;
		} else {
			$contador = 0;
		}
		stopTimer(); // Detener el temporizador cuando se responde
	};
	const handleNew = () => {
		$randomEmoji = random();
		$emojiDistinto = getRandomIndex();
		$respondido = false;
		$blur = false;
		startTimer();
	};

	const startTimer = () => {
			timer.set(duration);
		stopTimer(); // Asegurarse de que no haya un intervalo activo antes de iniciar uno nuevo
		interval = setInterval(() => {
			timer.update((n) => {
				if (n > 0) return n - 1;
				stopTimer();
				handleTimeOut();
				return n;
			});
		}, 1000);
	};

	const stopTimer = () => {
		if (interval) {
			clearInterval(interval);
			interval = null;
		}
	};

	const handleTimeOut = () => {
		$respondido = true;
		$correcto = false;
		$blur = true;
		$successLabel = '¡¡Más rápido, che!! 🐢🐢🐢';
		$contador = 0; // Reset the counter on timeout
		stopTimer(); // Asegurarse de que el temporizador se detenga
	};

	const toggleTimer = () => {
		if ($timerEnabled) {
			stopTimer();
		} else {
			startTimer();
		}
		$timerEnabled = !$timerEnabled; // Toggle the timer state
	};

	onDestroy(() => {
		stopTimer();
	});
</script>

<svelte:head>
	<title>Emojis</title>
	<meta name="description" content="Emojis" />
</svelte:head>

<div class="overflow-hidden absolute w-full h-full bg-gradient-to-r from-slate-900 to-slate-700">
	<Background />
	<div class="flex relative z-10 flex-col justify-center items-center w-full h-full">
		<div class="flex absolute w-full top-6 gap-2 justify-between px-6">
			<button on:click={toggleTimer} class="text-2xl" type="button">
					<span class={`${$timerEnabled ? 'saturate-0' : ''}`}>🧘🏽‍♀️</span>
					<span class={`${!$timerEnabled ? 'saturate-0' : ''}`}>⏰</span>
			</button>
			<p class="text-2xl font-extrabold text-slate-300">{`⭐ ${$contador}`}</p>
		</div>

		<p
			class="font-sans text-3xl font-bold text-center uppercase text-slate-300 text-w max-w-72 md:max-w-full"
		>
			Elegí el emoji distinto
		</p>
		{#if $randomEmoji}
			<div
				class={`grid grid-cols-6 gap-2 place-items-center px-4 py-3 my-3 rounded-xl border-4 border-gray-500 opacity-90 bg-slate-200 md:grid-cols-6 md:gap-6`}
			>
				{#each Array.from({ length: cantidad }) as _, i}
					<button on:click={() => handleClick(i)}>
						<p
							class={`emoji ${i === $emojiDistinto ? 'distinto' : ''} ${i === $emojiDistinto && $respondido && !$blur ? 'border-2 border-red-500 rounded-lg border-dashed' : ''}`}
						>
							{$randomEmoji.emoji}
						</p>
					</button>
				{/each}
			</div>
		{:else}
			<p class="col-span-6 row-span-3 text-2xl text-white">Cargando...</p>
		{/if}

		{#if $respondido}
			<div class="flex absolute justify-center items-center w-full h-full">
				<div
					role="button"
					tabindex="0"
					on:touchstart={() => ($blur = false)}
					on:touchend={() => ($blur = true)}
					class={`flex absolute justify-center items-center w-full h-full before:absolute before:w-full before:h-full ${$blur ? 'before:backdrop-blur-sm' : ''}`}
				></div>
				{#if $blur}
					<Button label={$successLabel} onClick={() => handleNew()} />
				{/if}
			</div>
		{:else}
			{#if $timerEnabled}
				<Timer progress={$timer} duration={duration} />
			{/if}
		{/if}
	</div>
</div>
