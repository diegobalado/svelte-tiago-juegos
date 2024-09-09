<script>
	import Background from '../components/Background.svelte';
	import { onMount } from 'svelte';
	import Button from '../components/Button.svelte';

	/**
	 * @typedef {Object} Question
	 * @property {number} id - The unique identifier for the question.
	 * @property {string} question - The quiz question text.
	 * @property {Array<string>} options - An array of answer options for the question.
	 * @property {number} answer - The index of the correct answer in the options array.
	 */

	/**
	 * @type {Question[]}
	 */
	let jsonData = [];
	let error = null;
	/**
	 * @type {Promise<any> | null}
	 */
	let currentQuestion = null;

	const getQuestion = (/** @type {string | any[]} */ jsonData) => {
		const index = getRandomInt(0, jsonData.length - 1);
		console.log({ index, jsonData, question: jsonData[index] });
		return jsonData[index];
	};

	/**
	 * @param {number} min
	 * @param {number} max
	 */
	function getRandomInt(min, max) {
		return Math.floor(Math.random() * (max - min + 1)) + min;
	}

	let data = null;
	let loading = true;

	// Función para cargar el JSON
	async function loadData() {
		try {
			const response = await fetch('/data/preguntas_1.json');
			if (!response.ok) {
				throw new Error('Error al cargar el JSON');
			}
			data = await response.json();
			currentQuestion = getQuestion(data);
		} catch (error) {
			console.error(error);
		} finally {
			loading = false;
		}
	}

	onMount(() => {
		loadData();
	});
</script>

<svelte:head>
	<title>Tiago Preguntas</title>
	<meta name="description" content="El juego de Tiago!" />
</svelte:head>

<div class="absolute h-full w-full overflow-hidden bg-gradient-to-r from-slate-900 to-slate-700">
	<Background />
	<div class="z-10 flex flex-col items-center">
		<h1 class="mt-6 pt-0 text-center text-2xl font-black uppercase text-slate-50">
			Bienvenid@ a Tiago Preguntas!!
		</h1>

		{#await currentQuestion}
			<p class="white">...cargando</p>
		{:then question}
			{#if question}
				<div
					class="mt-6 flex h-32 w-96 items-center justify-center rounded-xl border-2 border-slate-700 bg-slate-200"
				>
					<p class="text-center text-balance">
						{question.question}
					</p>
				</div>
				<div class="flex flex-col pt-4 gap-4">
					{#each question?.options as option}
						<Button label={option} />
					{/each}
				</div>
			{/if}
		{:catch error}
			<p style="color: red">{error.message}</p>
		{/await}
	</div>
</div>
