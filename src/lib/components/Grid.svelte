<script>
	export let device
	export let step = 0
	
	let manuallySelectedCell = null

	const rows = 2;
	const cols = 8;

	function clickCell(row, col) {
		manuallySelectedCell = row * cols + col
		const hold = device.parametersById.get('hold_position')
		hold.value = 1
		const override = device.parametersById.get('step_override')
		override.value = manuallySelectedCell 
	}

	function releaseCell() {
		manuallySelectedCell = null
		const param = device.parametersById.get('hold_position')
		param.value = 0
	}

	$: activeCell = manuallySelectedCell !== null ? manuallySelectedCell : step


</script>

<svelte:window on:mouseup={releaseCell} on:touchend={releaseCell} />

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
				/>
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
		background: rgb(214, 229, 53);
		color: white;
	}

	.active {
		background: #501a72;
	}
</style>