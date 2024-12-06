<script lang="ts">
	import { onMount } from 'svelte';

	// i = number of dropdowns, r = number of OR blocks, j = number of perks

	let dropdowns = $state([1]);
	let orblocks = $state([[1]]);
	let perkDropdowns = $state([[[['']]]]); // Array of arrays, each representing perk dropdowns for a person
	let perks: object = $state({});
	let chance: number = $state(0);
	let people: string[] = [''];
	let peopleText: string[][] = $state([['']]);
	let text: string = $state('');
	let output: string = $state('');

	const addDropdown = () => {
		dropdowns = [...dropdowns, dropdowns.length + 1];
		orblocks = [...orblocks, [orblocks.length + 1]];
		perkDropdowns = [...perkDropdowns, [[['']]]]; // Add a new array for each new person
		peopleText = [...peopleText, ['']];
	};

	const addORblock = (i: number) => {
		orblocks[i] = [...orblocks[i], orblocks.length + 1];
		perkDropdowns[i].push([['']]); // Add a new array for each new person
		buildOutput();
	};

	const addPerk = (i: number, r: number) => {
		perkDropdowns[i][r] = [...perkDropdowns[i][r], ['']];
		buildOutput();
	};

	const removePerk = (i: number, r: number, j: number) => {
		perkDropdowns[i][r] = [...perkDropdowns[i][r].slice(0, j), ...perkDropdowns[i][r].slice(j + 1)];
		buildOutput();
	};

	const removeDropdown = (i: number) => {
		dropdowns = [...dropdowns.slice(0, i), ...dropdowns.slice(i + 1)];
		orblocks = [...orblocks.slice(0, i), ...orblocks.slice(i + 1)];
		perkDropdowns = [...perkDropdowns.slice(0, i), ...perkDropdowns.slice(i + 1)];
		peopleText = [...peopleText.slice(0, i), ...peopleText.slice(i + 1)];
		buildOutput();
	};

	const removeORblock = (i: number, r: number) => {
		orblocks[i] = [...orblocks[i].slice(0, r), ...orblocks[i].slice(r + 1)];
		perkDropdowns[i] = [...perkDropdowns[i].slice(0, r), ...perkDropdowns[i].slice(r + 1)];
		buildOutput();
	};

	const buildOutput = () => {
		people = perkDropdowns.map(
			(person, i) =>
				person
					.map(
						(orblock) => orblock.join('|') // Join perks in each OR block
					)
					.join(' ') + (peopleText[i] ? ' ' + peopleText[i] : '')
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
			<h1 class="display-6 text-success"><img src="/favicon.png" alt="Cactus logo." />Cactus</h1>
		</div>
		<form onchange={buildOutput}>
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
					<button type="button" class="btn" onclick={addDropdown}> + </button>
				</div>
				{#each dropdowns as dropdown, i (dropdown)}
					<div class="row m-auto border p-2 rounded mb-3">
						<div class="d-flex align-items-center gap-3 mb-2">
							<button type="button" class="btn btn-primary btn-sm" onclick={() => addORblock(i)}>
								Add OR Block
							</button>
							<div class="form-check m-0">
								<input class="form-check-input" type="checkbox" id="or-not-{i}" />
								<label class="form-check-label" for="or-not-{i}">Negative (!)</label>
							</div>
							<button
								type="button"
								class="btn text-danger ms-auto align-self-end"
								style="font-size: x-large; padding: unset;"
								onclick={() => removeDropdown(i)}
							>
								✖</button
							>
						</div>
						{#each orblocks[i] as orblock, r (r)}
							<div class="row m-auto border p-2 rounded mb-3">
								<div class="d-flex align-items-center gap-3 mb-2">
									<button
										type="button"
										class="btn btn-primary btn-sm"
										onclick={() => addPerk(i, r)}
									>
										Add Perk
									</button>
									<button
										type="button"
										class="btn text-danger ms-auto align-self-end"
										style="font-size: x-large; padding: unset;"
										onclick={() => removeORblock(i, r)}
									>
										✖</button
									>
								</div>
								<div class="row m-auto justify-content-center">
									{#each perkDropdowns[i][r] as perkDropdown, j (j)}
										<div class="d-flex align-items-center col-lg-6 col-md-12">
											<select class="form-select w-auto m-2" bind:value={perkDropdowns[i][r][j]}>
												{#each Object.values(perks).sort( (a, b) => a.name.localeCompare(b.name) ) as perk}
													<option value={perk.name.replaceAll(' ', '_').replaceAll("'", '')}>
														{perk.name}
													</option>
												{/each}
											</select>
											<button
												type="button"
												class="btn text-danger"
												style="font-size: x-large;"
												onclick={() => removePerk(i, r, j)}
											>
												✖
											</button>
	
											<div class="form-check m-0">
												<input class="form-check-input" type="checkbox" id="perk-not-{i}-{r}-{j}" />
												<label class="form-check-label" for="perk-not-{i}-{r}-{j}">*</label>
											</div>
										</div>
									{/each}
								</div>
								<input
									type="text"
									class="form-control"
									placeholder="alive:3 members:2"
									bind:value={peopleText[i][r]}
								/>
							</div>
						{/each}
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
			<textarea
				id="output"
				name="output"
				disabled
				class="form-control scrollable-input"
				placeholder="You will see your output here"
				bind:value={output}
			></textarea>
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
