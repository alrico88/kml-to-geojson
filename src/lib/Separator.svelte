<script lang="ts">
	import type { Snippet } from 'svelte';

	const { title, icon }: { title: string; icon: Snippet } = $props();
</script>

<div class="hstack gap-3 align-items-center mb-2">
	<div class="hstack gap-2">
		<div>{@render icon()}</div>
		<div class="fw-bold text-nowrap">{title}</div>
	</div>
	<div class="w-100">
		<hr />
	</div>
</div>
