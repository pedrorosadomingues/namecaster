<script>
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import { onDestroy } from 'svelte';
	import { get } from 'svelte/store';

	export let data;

	let nameInput = data.name || '';
	let debounceTimer;

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

	{#if data.name && data.age !== null}
		<p>A idade estimada para <strong>{data.name}</strong> é <strong>{data.age}</strong> anos.</p>
	{:else if data.name}
		<p>Não foi possível estimar a idade para <strong>{data.name}</strong>.</p>
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
		border: 2px solid #aaa;
		border-radius: 8px;
		width: 100%;
		transition: border-color 0.3s;
	}

	input:focus {
		border-color: #0077ff;
		outline: none;
	}

	p {
		margin-top: 1.5rem;
		font-size: 1.2rem;
	}
</style>
