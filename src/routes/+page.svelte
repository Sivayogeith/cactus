<script lang="ts">
	import { onMount } from 'svelte';

	let dropdowns = [1];
	let perkDropdowns = [[['']]]; // Array of arrays, each representing perk dropdowns for a person
	let perks: object = {};
	let chance: number = 0;
	let people: string[] = [''];
	let peopleText: string[] = [''];
	let text: string = '';
	let output: string = '';

	const addDropdown = () => {
		dropdowns = [...dropdowns, dropdowns.length + 1];
		perkDropdowns = [...perkDropdowns, [['']]]; // Add a new array for each new person
		people = [...people, ''];
	};

	const addPerk = (index: number) => {
		perkDropdowns[index] = [...perkDropdowns[index], ['']];
		buildOutput();
	};

	const removePerk = (personIndex: number, perkIndex: number) => {
		perkDropdowns[personIndex].splice(perkIndex, 1); // Remove the selected perk
		perkDropdowns = [...perkDropdowns]; // Trigger reactivity
		buildOutput();
	};

	const buildOutput = () => {
		people = perkDropdowns.map(
			(person, i) => person.join(' ') + (peopleText.hasOwnProperty(i) ? ' ' + peopleText[i] : '')
		);
		output = `Game.act(${chance}, ${JSON.stringify(people)}, "${text}")`;
	};

	onMount(async () => {
		const perkRes = await fetch('/perks.json');
		perks = await perkRes.json();
	});
</script>

<div class="vh-100 d-flex justify-content-center font-monospace">
	<div class="w-100">
		<div class="bg-light p-1 text-center">
			<h1 class="display-6 text-success">Cactus</h1>
		</div>
		<form on:change={buildOutput}>
			<div class="container-md section mx-auto p-4">
				<label for="chance" class="form-label">Chance</label>
				<input
					type="number"
					id="chance"
					name="chance"
					class="form-control"
					placeholder="Enter your chance"
					max="1"
					bind:value={chance}
				/>
			</div>

			<div class="section mx-auto p-4">
				<div class="d-flex justify-content-between align-items-center">
					<label for="people" class="form-label">People</label>
					<button type="button" class="btn" on:click={addDropdown}> + </button>
				</div>
				{#each dropdowns as dropdown, i (dropdown)}
					<div class="row m-auto border p-2 rounded mb-3">
						<div class="d-flex justify-content-between mb-2">
							<button type="button" class="btn btn-primary btn-sm" on:click={() => addPerk(i)}
								>Add Perk</button
							>
						</div>
						<div class="row m-auto justify-content-center">
							{#each perkDropdowns[i] as perkDropdown, j (j)}
								<div class="d-flex align-items-center col-lg-6 col-md-12">
									<select class="form-select w-auto m-2" bind:value={perkDropdowns[i][j]}>
										{#each Object.values(perks).sort( (a, b) => a.name.localeCompare(b.name) ) as perk}
											<option value={perk.name.replaceAll(' ', '_').replaceAll("'", '')}
												>{perk.name}</option
											>
										{/each}
									</select>
									<button
										type="button"
										class="btn text-danger"
										style="font-size: x-large;"
										on:click={() => removePerk(i, j)}
									>
										✖
									</button>
								</div>
							{/each}
						</div>
						<input
							type="text"
							class="form-control"
							placeholder="alive:3 members:2"
							bind:value={peopleText[i]}
						/>
					</div>
				{/each}
			</div>

			<div class="container-md section mx-auto p-4">
				<label for="text" class="form-label">Text</label>
				<input
					type="text"
					id="text"
					name="text"
					class="form-control"
					placeholder="Enter your text"
					bind:value={text}
				/>
			</div>
		</form>

		<div class="container-md section mx-auto p-4">
			<label for="output" class="form-label">Output</label>
			<input
				type="text"
				id="output"
				name="output"
				disabled
				class="form-control"
				placeholder="You will see your output here"
				bind:value={output}
			/>
		</div>
	</div>
</div>

<style>
	.section {
		width: 50%;
	}
	@media (max-width: 1500px) {
		.section {
			width: 75%;
		}
	}
	@media (max-width: 748px) {
		.section {
			width: 100%;
		}
	}
</style>
