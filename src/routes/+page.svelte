<script lang="ts">
	import { onMount } from 'svelte';

	let dropdowns = [1];
	let perkDropdowns = [[1]];  // Array of arrays, each representing perk dropdowns for a person
	let perks: object = {};

	const addDropdown = () => {
		dropdowns = [...dropdowns, dropdowns.length + 1];
		perkDropdowns = [...perkDropdowns, [1]];  // Add a new array for each new person
	};

	const addPerk = (index: any) => {
		perkDropdowns[index] = [...perkDropdowns[index], perkDropdowns[index].length + 1];
	};

	onMount(async () => {
		const response = await fetch('/perks.json');
		perks = await response.json();
	});
</script>

<div class="vh-100 d-flex justify-content-center font-monospace">
	<div class="w-100">
		<div class="bg-light p-1 text-center">
			<h1 class="display-6 text-success">Cactus</h1>
		</div>
		<form>
			<div class="w-50 mx-auto p-4">
				<label for="chance" class="form-label">Chance</label>
				<input
					type="number"
					id="chance"
					name="chance"
					class="form-control"
					placeholder="Enter your chance"
					max="1"
				/>
			</div>

			<div class="w-50 mx-auto p-4">
				<div class="d-flex justify-content-between align-items-center">
					<label for="people" class="form-label">People</label>
					<button type="button" class="btn" on:click={addDropdown}> + </button>
				</div>
				{#each dropdowns as dropdown, i (dropdown)}
					<div class="row m-auto border p-2 rounded mb-3">
						<button type="button" class="btn btn-primary btn-sm w-25" on:click={() => addPerk(i)}>Add Perk</button>
						<div class="row m-auto justify-content-center">
							{#each perkDropdowns[i] as perkDropdown (perkDropdown)}
								<select class="form-select col-md-4 w-auto m-2">
									{#each Object.values(perks) as perk}
										<option value={perk.id}>{perk.name}</option>
									{/each}
								</select>
							{/each}
						</div>
					</div>
				{/each}
			</div>

			<div class="w-50 mx-auto p-4">
				<label for="text" class="form-label">Text</label>
				<input
					type="text"
					id="text"
					name="text"
					class="form-control"
					placeholder="Enter your text"
				/>
			</div>
		</form>
	</div>
</div>