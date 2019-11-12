<script>
	import EditableLine from './EditableLine.svelte';
	import {tick} from 'svelte';
	let name = 'world';	
	let fileContent = "";
	let data = "";
	let allLines = [];
	let vttRegEx = /\d+:\d+:\d+\.\d+-->\d+:\d+:\d+\.\d+/;
	let currentFileName = "data";
	let hideTimeCode = false;
	let videoSrc = "https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_2mb.mp4";
	let player;
	let encodedVtt;
	let joinedLines = "";
	
	function readFile(ev) {
		let fr = new FileReader();
		fr.onload = function(evt) {
			fileContent = evt.target.result;
  	};
		if(ev.target && ev.target.files[0] != null) {
			fr.readAsText(ev.target.files[0]);
			currentFileName = ev.target.files[0].name;
		}
	}
	
	function toUri(utfString) {
		return encodeURI(utfString);
	}
	
	function joinLines(lineArray) {
		return lineArray.join("\n");
	}
	
	function jumpToPosition(line) {
		if(!player) return;
		let time = line.split(':');
		let seconds = Number.parseFloat(time[0] * 3600 + time[1] * 60 + time[2]);
		player.currentTime = seconds;
	}
	
	function getId(number) {
		blob[0].id = Number.parseInt(Math.random()*20000);
		return "test";
	}
	
	function waitForBlob(thedata) {
		 return new Promise((resolve, reject) => {
			setTimeout(() => {
				setTimeout(() => {
					if(player.textTracks && player.textTracks.length > 0)
						player.textTracks[0].mode = "showing";
				},50);
				let blob = URL.createObjectURL(new Blob([joinedLines],{type:"text/html"}))
				resolve(blob);
			}
			,100);
  	});
	}
	
	$: if(fileContent !== "") allLines = fileContent.split('\n');	
	$: if(allLines.length > 0) joinedLines =  joinLines(allLines);
	$: data = "text/json;charset=utf-8," + toUri(joinedLines);
	
</script>
<label>video url</label><input type="text" bind:value={videoSrc} />
<label>subtitle file</label><input type="file" on:change={readFile} accept=".vtt" />
<label>hide timecode</label><input type="checkbox" bind:checked={hideTimeCode} />

{#if data !== ""}
	<a href={`data:${data}`} download={`${currentFileName}`}>download</a>
{/if}

{#each allLines as line, index}
	{#if line !== "" && !vttRegEx.test(line) && index > 3}
		<EditableLine bind:line={line}/>
	{:else}
		<div class="timecode" class:hideTimeCode on:click={() => jumpToPosition(line)} >
			{line}	
		</div>
	{/if}
{/each}

<video bind:this={player} controls class="player" src={videoSrc}>
	{#await waitForBlob(joinedLines) then blob}
		<track label="german" kind="subtitles" srclang="de" src={blob}/>
	{/await}
</video>

<style>
	div {
		color: black;
		margin: 0;
	}
	.timecode {
		color: grey;
		cursor: pointer;
		font-size: 0.7em;
	}
	
	input {
		display: block;
	}
	
	.hideTimeCode {
		opacity: 0;
		
	}
	
	.player {
		background-color: grey;
		position: fixed;
		right: 20px;
		top: 80px;
		width: 40%;
	}
	
	a {
		display: block;
		margin: 30px;
	}
	
	:global(body) {
		font-size: 1em;
	}
</style>


