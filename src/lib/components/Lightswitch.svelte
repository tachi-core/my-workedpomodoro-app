<!-- <script lang="ts">
	let isDark = false;

	import IconSun from '@lucide/svelte/icons/sun';
	import IconMoon from '@lucide/svelte/icons/moon';
	import { onMount } from 'svelte';

	// Saat pertama dimuat, ambil dari localStorage atau system preference
	onMount(() => {
		const savedMode = localStorage.getItem('mode');
		if (savedMode) {
			isDark = savedMode === 'dark';
		} else {
			isDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
		}
		setMode(isDark);
	});

	// Ubah mode dan simpan ke localStorage
	function toggleMode() {
		isDark = !isDark;
		setMode(isDark);
	}

	function setMode(dark: boolean) {
		const mode = dark ? 'dark' : 'light';
		document.documentElement.setAttribute('data-mode', mode);
		localStorage.setItem('mode', mode);
	}
</script>

<svelte:head>
	<script>
		const saved = localStorage.getItem('mode');
		const mode =
			saved || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
		document.documentElement.setAttribute('data-mode', mode);
	</script>
</svelte:head>

<button
	type="button"
	class="btn-icon preset-filled rounded-full active:scale-[0.98] active:transition-all"
	on:click={toggleMode}
	aria-label="Toggle Dark Mode"
>
	{#if isDark}
	<IconSun size="20" fill="#0d0d0d" />
	{:else}
	<IconMoon size="20" color="#fcfcfc" fill="#fcfcfc" />
	{/if}
</button> -->

<!-- <script lang="ts">
  import { Switch } from '@skeletonlabs/skeleton-svelte';
  // Icons
  import IconMoon from '@lucide/svelte/icons/moon';
  import IconSun from '@lucide/svelte/icons/sun';

  // Bind to the checked state
  let mode = $state(false);

  // Handle the change in state when toggled.
  function handleModeChange(checked: boolean) {
    // NOTE: implementation may differ per Tailwind config and framework:
    // https://tailwindcss.com/docs/dark-mode#toggling-dark-mode-manually
    console.log({ mode });
    mode = checked;
  }
</script>

<Switch name="mode" controlActive="bg-surface-200" checked={mode} onCheckedChange={(e) => handleModeChange(e.checked)}>
  {#snippet inactiveChild()}<IconMoon size="14" />{/snippet}
  {#snippet activeChild()}<IconSun size="14" />{/snippet}
</Switch> -->

<script lang="ts">
	import { onMount } from 'svelte';
	import { Switch } from '@skeletonlabs/skeleton-svelte';
	import IconMoon from '@lucide/svelte/icons/moon';
	import IconSun from '@lucide/svelte/icons/sun';

	let mode = false;

	onMount(() => {
		const savedMode = localStorage.getItem('theme');
		if (savedMode === 'light') {
			mode = true;
			document.documentElement.setAttribute('data-mode', 'light');
		} else {
			mode = false;
			document.documentElement.setAttribute('data-mode', 'dark');
		}
	});

	function handleModeChange(checked: boolean) {
		mode = checked;
		const modeValue = checked ? 'light' : 'dark';
		document.documentElement.setAttribute('data-mode', modeValue);
		localStorage.setItem('theme', modeValue);
	}
</script>

<Switch
	name="mode"
	checked={mode}
	onCheckedChange={(e) => handleModeChange(e.checked)}
	controlInactive="bg-surface-100"
	controlActive="bg-surface-300"

	
>
	{#snippet inactiveChild()}<IconMoon size="14" />{/snippet}
	{#snippet activeChild()}<IconSun size="14" />{/snippet}
</Switch>
