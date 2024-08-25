<script lang="ts">
	import CodeMirror from 'svelte-codemirror-editor';
	import { json } from '@codemirror/lang-json';
	import CopyButton from './CopyButton.svelte';
	import DownloadButton from './DownloadButton.svelte';
	import { countBy } from 'lodash-es';
	import type { FeatureCollection } from 'geojson';
	import Separator from './Separator.svelte';

	const { geojsonStr }: { geojsonStr: string } = $props();

	const parsed = $derived(JSON.parse(geojsonStr) as FeatureCollection);

	const countByFeatures = $derived.by(() => {
		const count = countBy(parsed.features, (d) => d.geometry.type);

		return Object.entries(count).map(([geomType, count]) => {
			return {
				geomType,
				count
			};
		});
	});
</script>

{#snippet statsIcon()}
	<img src="/icons/file.svg" alt="Statistics" width="20" />
{/snippet}

<div class="vstack gap-2">
	<div class="maxHeight">
		<CodeMirror
			lang={json()}
			class="border h-100 bg-white"
			value={geojsonStr}
			editable={false}
			lineWrapping={true}
		/>
	</div>
	<div class="hstack gap-2">
		<CopyButton text={geojsonStr} />
		<DownloadButton text={geojsonStr} />
	</div>
	<div class="row">
		<div class="col">
			<Separator title="Statistics" icon={statsIcon} />
			<div class="list-group">
				<div class="list-group-item">
					<h5>Features found</h5>
					<div>{parsed.features.length}</div>
				</div>
				<div class="list-group-item">
					<h5>Count by geometry type</h5>
					<ul class="mb-0">
						{#each countByFeatures as item}
							<li>
								<span class="fw-bold">{item.geomType}:</span>
								{item.count}
							</li>
						{/each}
					</ul>
				</div>
			</div>
		</div>
	</div>
</div>

<style>
	.maxHeight {
		height: 400px !important;
	}

	:global(.cm-editor) {
		height: 100%;
	}

	:global(.cm-scroller) {
		overflow: auto;
	}
</style>
