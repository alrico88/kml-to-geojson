<script lang="ts">
	import { readAsText } from 'promise-file-reader';
	import type { ChangeEventHandler } from 'svelte/elements';
	import { kml } from '@tmcw/togeojson';
	import xmldom from '@xmldom/xmldom';
	import { Formatter } from 'fracturedjsonjs';
	import GeoJsonResult from '$lib/GeoJSONResult.svelte';
	import is from '@sindresorhus/is';
	import '@fontsource-variable/inter';
	import Separator from '$lib/Separator.svelte';
	import Credits from '$lib/Credits.svelte';

	let text = $state('');
	let geojsonResult = $state('');

	const disabledConvert = $derived(is.emptyStringOrWhitespace(text));

	const handleFileChange: ChangeEventHandler<HTMLInputElement> = async (e) => {
		geojsonResult = '';

		if (is.nullOrUndefined(e.currentTarget.files)) {
			text = '';

			return;
		}

		text = await readAsText(e.currentTarget.files[0]);
	};

	function convert() {
		const dom = new xmldom.DOMParser().parseFromString(text, 'text/xml');

		const fmt = new Formatter();

		geojsonResult = fmt.Serialize(kml(dom)) as string;
	}
</script>

{#snippet geojsonIcon()}
	<img src="/icons/geojson.svg" alt="Result" width="20" />
{/snippet}

{#snippet kmlIcon()}
	<img src="/icons/kml.svg" alt="KML" width="20" />
{/snippet}

{#snippet creditsIcon()}
	<img src="/icons/about.svg" alt="About" width="20" />
{/snippet}

<svelte:head>
	<title>KML to GeoJSON converter</title>
	<meta name="description" content="Convert KML to GeoJSON online for free" />
	<meta name="keywords" content="kml to geojson,kml,geojson,free,online,private,convert" />
</svelte:head>
<div class="container vstack gap-2 pb-3">
	<div class="row">
		<div class="col py-5">
			<h1 class="fw-bold">KML to GeoJSON converter</h1>
			<div class="lead">Convert KML files to GeoJSON privately</div>
		</div>
	</div>
	<section>
		<Separator title="File to convert" icon={kmlIcon} />
		<div class="row">
			<div class="col">
				<form class="vstack gap-2" onsubmit={convert}>
					<div class="form-group">
						<label class="form-label" for="fileInput">Choose a KML file</label>
						<input
							class="form-control"
							type="file"
							id="fileInput"
							accept=".kml"
							autocomplete="off"
							onchange={handleFileChange}
						/>
					</div>
					<button class="btn btn-primary w-100" type="submit" disabled={disabledConvert}
						>Convert</button
					>
				</form>
			</div>
		</div>
	</section>
	<section>
		<Separator title="Result" icon={geojsonIcon} />
		<div class="row">
			<div class="col">
				{#if geojsonResult !== ''}
					<GeoJsonResult geojsonStr={geojsonResult} />
				{:else}
					<div class="alert alert-warning mb-0">First select a KML file and click convert</div>
				{/if}
			</div>
		</div>
	</section>
	<section>
		<Separator title="Credits" icon={creditsIcon} />
		<Credits />
	</section>
</div>

<style>
	h1 {
		color: #e36002;
	}

	:global(.btn) {
		border-radius: 20px;
	}

	:global(body) {
		font-family: 'Inter Variable', sans-serif;
		background: #f8fafd;
		min-height: 100vh;
	}
</style>
