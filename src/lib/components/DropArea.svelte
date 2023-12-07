<script>
	import { createEventDispatcher } from 'svelte'

	const dispatch = createEventDispatcher()

	let hoveringDragArea = false
	let fileInput

	function handleDragOver(event) {
		event.preventDefault()
		event.dataTransfer.dropEffect = 'copy'
		hoveringDragArea = true
	}

	function handleDragLeave(event) {
		event.preventDefault()
		hoveringDragArea = false
	}

	function handleDrop(event) {
		event.preventDefault()
		hoveringDragArea = true

		const file = event.dataTransfer.files[0];
		handleFile(file)
	}

	function chooseFile() {
		fileInput.click();
	}

	function handleFileSelection(event) {
		const file = event.target.files[0];
		handleFile(file);
	}

	function handleFile(file) {
		if (file && file.type === 'audio/x-wav') {
			const reader = new FileReader();

			reader.onload = function (e) {
				const arrayBuffer = e.target.result;
				handleArrayBuffer(arrayBuffer)
			};

			reader.readAsArrayBuffer(file);
		} else {
			alert('Please select a valid WAV file.');
		}
	}

	function handleArrayBuffer(arrayBuffer) {
		dispatch('read', arrayBuffer)
	}
</script>

<div id='drop-area' on:dragover={handleDragOver} on:dragleave={handleDragLeave} on:drop={handleDrop} class:highlight={hoveringDragArea}>
	<p>drag a breakbeat here</p>
	<input bind:this={fileInput} type="file" id="file-input" accept="audio/x-wav" on:change={handleFileSelection}>
</div>

<style>
    #drop-area {
		width: 300px;
		height: 200px;
		border: 1px dashed #ccc;
		border-radius: 0;
		text-align: center;
		margin: 50px auto;
		cursor: pointer;
    }

    .highlight {
		border-color: #2185d0;
		color: #2185d0;
    }
</style>