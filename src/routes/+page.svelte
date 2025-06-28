<script>
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import { onDestroy } from 'svelte';
	import { get } from 'svelte/store';
	import { fly } from 'svelte/transition';

	export let data;

	let nameInput = '';
	let debounceTimer;

	function capitalize(str) {
		if (!str) return '';
		return str.charAt(0).toUpperCase() + str.slice(1);
	}

	function handleInput(event) {
		const target = event.target;
		const newName = target.value;

		nameInput = newName;

		if (debounceTimer) {
			clearTimeout(debounceTimer);
		}

		debounceTimer = setTimeout(() => {
			const currentParams = get(page).url.searchParams;
			const current = currentParams.get('name') || '';

			// Só atualiza se mudou
			if (newName.trim() && newName !== current) {
				const query = new URLSearchParams({ name: newName.trim() });
				goto(`/?${query.toString()}`, { replaceState: true });
			} else if (!newName.trim() && current) {
				goto('/', { replaceState: true });
			}
		}, 700);
	}

	onDestroy(() => {
		if (debounceTimer) clearTimeout(debounceTimer);
	});

</script>

<main>
	<h1>Descubra a idade estimada por nome</h1>
	<input
		type="text"
		bind:value={nameInput}
		on:input={handleInput}
		placeholder="Digite um nome..."
		autocomplete="off"
	/>
	{#if data.name && data.age !== null && data.name !== ''}
		<p transition:fly={{ x: -60, duration: 800 }}>
			A idade estimada para <strong>{capitalize(data.name)}</strong> é <strong>{data.age}</strong> anos.
		</p>
	{:else if data.name}
		<p transition:fly={{ x: -60, duration: 800 }}>
			Não foi possível estimar a idade para <strong>{capitalize(data.name)}</strong>.
		</p>
	{/if}
</main>

<style>
	main {
		max-width: 500px;
		margin: 5rem auto;
		text-align: center;
		font-family: system-ui, sans-serif;
	}

	input {
		padding: 0.75rem 1rem;
		font-size: 1rem;
		border: 2px solid black;
		border-radius: 8px;
		width: 100%;
		transition: border-color 0.3s;
		background-color: #ff2f2f;
	}

	input::placeholder {
		color: black;
	}

	input:focus {
		border-color: #0077ff;
		outline: none;
		border-width: 3px;
	}

	p {
		margin-top: 1.5rem;
		font-size: 1.2rem;
	}

	h1 {
		font-size: 2rem;
		margin-bottom: 1.5rem;
		color: #333;
		font-style: italic;
	}
</style>
