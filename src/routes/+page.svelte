<script>
	import * as RNBO from '@rnbo/js'
	import { createDeviceInstance } from '../lib/rnbo-helpers';
	import DropArea from '../lib/components/DropArea.svelte';
	import Grid from '../lib/components/Grid.svelte';
	let loaded = false;
	let device;
	let context;
	let step = 0;


	async function start() {
        context = new (window.AudioContext || window.webkitAudioContext)();
        let output = context.createGain().connect(context.destination);
        createDeviceInstance('/code/patch.export.json', context, output)
		.then(async d => {
			device = d
			device.messageEvent.subscribe((e) => {
				if (e.tag === 'step') {
					step = e.payload
				}
			}) 

			fetch('/sounds/amen.mp3')
				.then(response => response.arrayBuffer())
				.then(buffer => context.decodeAudioData(buffer))
				.then(audioBuf => device.setDataBuffer('src', audioBuf))
		})
		loaded = true
    };
	

</script>

{#if !loaded}
	<button on:click={start}>start</button>
{:else}
	{ step }
	<DropArea on:read={ async (event) => { 
		//  event.detail
		const decoded = await context.decodeAudioData(event.detail)
		device.setDataBuffer('src', decoded)
	}}
	/>

	<Grid bind:step={step} bind:device={device}/>
{/if}