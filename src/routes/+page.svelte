<script lang="ts">
	/**
	 * Developer Notes
	 * console.log does not work here, use $inspect(variable) outside any function so that every time the variable is changed it will log it in the console
	 */

	import { onMount } from 'svelte';

	/**
	 * @state personBlocks - Array tracking the number of person blocks
	 * @state orBlocks - Array of OR blocks for each person
	 * @state perkDropdowns - Nested array representing perk person blocks for each person
	 * @state perks - Object storing perk data
	 * @state chance - Chance value used in the game action
	 * @state people - Array representing a person in the Murder Games Act.
	 * @state peopleText - Array of text inputs associated with each OR block
	 * @state perkNegative - Array of each perk
	 * @state text - Text input that may be included in the output string
	 * @state output - Final output string formatted for the game action
	 */
	let personBlocks: number[] = $state([0]);
	let orBlocks: boolean[][] = $state([[false]]);
	let perkDropdowns: string[][][][] = $state([[[['']]]]);
	let perks: object = $state({});
	let chance: number = $state(0);
	let people: string[] = [''];
	let peopleText: string[][] = $state([['']]);
	let perkNegative: boolean[][][] = $state([[[false]]]);
	let text: string = $state('');
	let output: string = $state('');

	/**
	 * Adds a new person block, OR block, perk, and peopleText for a new person.
	 * Rebuilds the output after adding the new elements.
	 */
	const addPersonBlock = () => {
		personBlocks.push(0);
		orBlocks.push([false]);
		perkDropdowns.push([[['']]]);
		peopleText.push(['']);
		perkNegative.push([[false]]);
		buildOutput();
	};

	/**
	 * Adds a new OR block for the specified person.
	 * Rebuilds the output after adding the new OR block.
	 * @param {number} i - Index of the person to add the OR block for
	 */
	const addORblock = (i: number) => {
		orBlocks[i].push(false);
		perkDropdowns[i].push([['']]);
		perkNegative[i].push([false]);
		buildOutput();
	};

	/**
	 * Adds a new perk to the specified OR block for a person.
	 * Rebuilds the output after adding the new perk.
	 * @param {number} i - Index of the person to add the perk for
	 * @param {number} r - Index of the OR block to add the perk to
	 */
	const addPerk = (i: number, r: number) => {
		perkDropdowns[i][r].push(['']);
		perkNegative[i][r].push(false);
		buildOutput();
	};

	/**
	 * Removes the specified person block, OR block, perk, and associated text entry for a person.
	 * Rebuilds the output after removal.
	 * @param {number} i - Index of the person block to remove
	 */
	const removePersonBlock = (i: number) => {
		personBlocks.splice(i, 1);
		orBlocks.splice(i, 1);
		perkDropdowns.splice(i, 1);
		peopleText.splice(i, 1);
		perkNegative.splice(i, 1);
		buildOutput();
	};

	/**
	 * Removes the specified OR block for a specific person.
	 * Rebuilds the output after removal.
	 * @param {number} i - Index of the person to remove the OR block for
	 * @param {number} r - Index of the OR block to remove
	 */
	const removeORblock = (i: number, r: number) => {
		orBlocks[i].splice(r, 1);
		perkDropdowns[i].splice(r, 1);
		perkNegative[i].splice(r, 1);
		buildOutput();
	};

	/**
	 * Removes the specified perk from a specific OR block for a person.
	 * Rebuilds the output after removal.
	 * @param {number} i - Index of the person
	 * @param {number} r - Index of the OR block
	 * @param {number} j - Index of the perk to remove
	 */
	const removePerk = (i: number, r: number, j: number) => {
		perkDropdowns[i][r].splice(j, 1);
		perkNegative[i][r].splice(j, 1);
		buildOutput();
	};

	/**
	 * Rebuilds the output string based on the current state of personBlocks, OR blocks, and perks.
	 * The output is formatted and includes the chance, people data, and text.
	 */
	const buildOutput = () => {
		people = perkDropdowns.map((person, i) =>
			person
				.map((orblock, r) => {
					let prefix = orBlocks[i][r] ? '!' : '';
					return prefix + perkNegative[i][r].map((pn, j) => (pn ? '*' + orblock[j] : orblock[j])).join('|') +
						(peopleText[i][r] ? ' ' + peopleText[i][r] : '');
				})
				.join(' ')
		);

		output = `new Game.act(${chance}, ${JSON.stringify(people)}, "${text}")`;
	};

	/**
	 * Fetches the perk data from a JSON file when the component is mounted.
	 * Stores the fetched perk data in the `perks` state variable.
	 */
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
					<button type="button" class="btn" onclick={addPersonBlock}> + </button>
				</div>
				{#each personBlocks as personBlock, i (i)}
					<div class="row m-auto border p-2 rounded mb-3">
						<div class="d-flex align-items-center gap-3 mb-2">
							<button type="button" class="btn btn-primary btn-sm" onclick={() => addORblock(i)}>
								Add OR Block
							</button>
							<button
								type="button"
								class="btn text-danger ms-auto align-self-end"
								style="font-size: x-large; padding: unset;"
								onclick={() => removePersonBlock(i)}
							>
								✖</button
							>
						</div>
						{#each orBlocks[i] as orblock, r (r)}
							<div class="row m-auto border p-2 rounded mb-3">
								<div class="d-flex align-items-center gap-3 mb-2">
									<button
										type="button"
										class="btn btn-primary btn-sm"
										onclick={() => addPerk(i, r)}
									>
										Add Perk
									</button>
									<div class="form-check m-0">
										<input
											class="form-check-input"
											type="checkbox"
											id="or-not-{i}"
											bind:checked={orBlocks[i][r]}
										/>
										<label class="form-check-label" for="or-not-{i}-{r}">Negative (!)</label>
									</div>
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
													<option value={perk.name.replaceAll(' ', '_')}>
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
												<input
													class="form-check-input"
													type="checkbox"
													id="perk-not-{i}-{r}-{j}"
													bind:checked={perkNegative[i][r][j]}
												/>
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
