<script>
	export let device
	export let step = 0
	
	let manuallySelectedCell = null

	const rows = 2;
	const cols = 8;

	function clickCell(row, col) {
		manuallySelectedCell = row * cols + col
		const hold = device.parametersById.get('hold_position')
		const override = device.parametersById.get('step_override')
		override.value = manuallySelectedCell 
		hold.value = 1
	}

	function releaseCell() {
		manuallySelectedCell = null
		const param = device.parametersById.get('hold_position')
		param.value = 0
	}

	$: activeCell = manuallySelectedCell !== null ? manuallySelectedCell : step

	const keyboardControls = ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'a', 's', 'd', 'f', 'g', 'h', 'j', 'k'];
	function handleKey(e) {
		
		// check if the key exists in the map
		if (keyboardControls.includes(e.key)) {
			const index = keyboardControls.indexOf(e.key)
			clickCell(Math.floor(index / cols), index % cols)
		}
	}

	function handleKeyRelease(e) {
		releaseCell()
	}

</script>

<svelte:window on:mouseup={releaseCell} on:touchend={releaseCell} on:keydown={handleKey} on:keyup={handleKeyRelease} />

<div>
	{#each Array.from({ length: rows }) as _, row}
		<div class="row">
			{#each Array.from({ length: cols }) as _, col}
				<div class="cell"
					class:active={activeCell === row * cols + col}
					on:mousedown={() => clickCell(row, col)}
					on:touchstart={() => clickCell(row, col)}
					on:mouseup={() => releaseCell()}
					on:touchend={() => releaseCell()}
				>
				{ keyboardControls[row * cols + col] }
				</div>
			{/each}
		</div>
	{/each}
</div>

<style>
	.row {
		display: flex;
		flex-direction: row;
		gap: 8px;
		margin: 8px;
	}
	.cell {
		width: 64px;
		height: 64px;
		background: rgb(49, 109, 243);
		color: white;
		text-align: center;
		align-content: center;
	}

	.active {
		background: rgb(191, 87, 208);
	}
</style>