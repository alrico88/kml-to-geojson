<script lang="ts">
	let { text }: { text: string } = $props();

	let copied = $state(false);

	function handleCopy() {
		copied = true;

		setTimeout(() => {
			copied = false;
		}, 2000);

		navigator.clipboard.writeText(text);
	}

	const btnText = $derived(copied ? 'Copied' : 'Copy to clipboard');
</script>

<button class="btn btn-primary w-100" onclick={handleCopy}>{btnText}</button>
