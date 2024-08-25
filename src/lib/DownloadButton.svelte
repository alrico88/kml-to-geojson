<script lang="ts">
	import fileSaver from 'file-saver';

	const { saveAs } = fileSaver;

	let { text }: { text: string } = $props();

	function handleDownload() {
		saveAs(new Blob([text]), 'kml-to-geojson-converted.geojson');
	}
</script>

<button class="btn btn-primary w-100" onclick={handleDownload}>Download</button>
