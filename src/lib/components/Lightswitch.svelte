<script lang="ts">
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
</button>