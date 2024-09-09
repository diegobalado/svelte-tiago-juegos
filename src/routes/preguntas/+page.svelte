<script>
	import Background from '../../components/background.svelte';
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
		<div
			class="mt-6 flex h-32 w-96 items-center justify-center rounded-xl border-2 border-slate-700 bg-slate-200"
		>
			Pregunta?
		</div>
	</div>
</div>
