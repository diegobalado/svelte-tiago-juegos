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
	 * @property {Category} category - The category of the question.
	 */

	/**
	* @typedef {'Todas' | 'Historia' | 'Geografia' | 'Ciencia' | 'Entretenimiento' | 'Deportes' | 'General'} Category
	 */
	const Category = {
		Todas: 'Todas',
		Historia: 'Historia',
		Geografia: 'Geografia',
		Ciencia: 'Ciencia',
		Entretenimiento: 'Entretenimiento',
		Deportes: 'Deportes',
		General: 'General'
	};

	/**
	 * @type {Question[]}
	 */
	let jsonData = [];
	let error = null;
	/**
	 * @type {Question | null}
	 */
	let currentQuestion = null;
	/**
	 * @type {string[]}
	 */
	let categories = [];
	/**
	 * @type {string}
	 */
	let selectedCategory = '';
	const getQuestion = (/** @type {Question[]} */ data, /** @type {string} */ category) => {
		const questionByCategory = category && category !== Category.Todas ? data.filter(q => q.category === category) : data;
		const randomIndex = getRandomInt(0, questionByCategory.length - 1);
		const question = { ...questionByCategory[randomIndex] };
		question.options = shuffleArray([...question.options]);
		question.answer = question.options.indexOf(questionByCategory[randomIndex].options[questionByCategory[randomIndex].answer]);
		console.log({ index: randomIndex, filteredData: questionByCategory, question });
		return question;
	};

	/**
	 * @param {number} min
	 * @param {number} max
	 */
	function getRandomInt(min, max) {
		return Math.floor(Math.random() * (max - min + 1)) + min;
	}

	/**
	 * @param {Array<T>} array
	 * @template T
	 */
	function shuffleArray(array) {
		for (let i = array.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[array[i], array[j]] = [array[j], array[i]];
		}
		return array;
	}

	/**
	 * @type {Question[] | null}
	 */
	let data = null;
	let loading = true;

	async function loadData() {
		try {
			const response = await fetch(`/data/preguntas.json`);
			if (!response.ok) {
				throw new Error('Error al cargar el JSON');
			}
			data = await response.json();
			categories = ['Todas', ...new Set(data.map(q => q.category))];
			selectedCategory = categories[0];
			currentQuestion = getQuestion(data, selectedCategory);
		} catch (error) {
			console.error(error);
		} finally {
			loading = false;
		}
	}

	onMount(() => {
		loadData();
	});

	/**
	 * @param {Question} question
	 * @param {number} index
	 */
	const checkAnswer = (question, index) => {
		if (index === question.answer) {
			alert('Correcto');
		} else {
			alert('Incorrecto');
		}
		// Load a new question after showing the result
		if (data) {
			currentQuestion = getQuestion(data, selectedCategory);
		}
	};

	/**
	 * @param {Event} event
	 */
	function handleCategoryChange(event) {
		selectedCategory = /** @type {HTMLSelectElement} */ (event.target).value;
		if (data) {
			currentQuestion = getQuestion(data, selectedCategory);
		}
	}
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

		{#if loading}
			<p class="white">...cargando</p>
		{:else if currentQuestion}
			<select
				class="mt-4 p-2 rounded-md"
				bind:value={selectedCategory}
				on:change={handleCategoryChange}
			>
				{#each categories as category}
					<option value={category}>{category}</option>
				{/each}
			</select>
			<div
				class="mt-6 flex h-32 w-96 items-center justify-center rounded-xl border-2 border-slate-700 bg-slate-200"
			>
				<p class="text-center text-balance">
					{currentQuestion.question}
				</p>
			</div>
			<div class="flex flex-col pt-4 gap-4">
				{#each currentQuestion.options as option, index}
					<Button label={option} onClick={() => checkAnswer(currentQuestion, index)} />
				{/each}
			</div>
		{:else if error}
			<p style="color: red">{error.message}</p>
		{/if}
	</div>
</div>
