<script lang="ts">
	// Kode Light switch jika menggunakan Switch
	// import { Switch } from '@skeletonlabs/skeleton-svelte';

	// let checked = $state(false);

	// $effect(() => {
	// 	const mode = localStorage.getItem('mode') || 'light';
	// 	checked = mode === 'dark';
	// });

	// const onCheckedChange = (event: { checked: boolean }) => {
	// 	const mode = event.checked ? 'dark' : 'light';
	// 	document.documentElement.setAttribute('data-mode', mode);
	// 	localStorage.setItem('mode', mode);
	// 	checked = event.checked;
	// };

	let isDark = false;

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
	<!--  Kode Light switch jika menggunakan Switch -->
	<!-- <script>
    const mode = localStorage.getItem('mode') || 'light';
    document.documentElement.setAttribute('data-mode', mode);
  </script> -->

	<script>
		const saved = localStorage.getItem('mode');
		const mode =
			saved || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
		document.documentElement.setAttribute('data-mode', mode);
	</script>
</svelte:head>

<!-- <Switch {checked} {onCheckedChange}></Switch> -->

<!-- <button
	type="button"
	class="flex h-8 w-8 cursor-pointer items-center justify-center rounded-full bg-slate-300 text-slate-800 active:scale-[0.98] active:transition-all dark:bg-slate-700 dark:text-slate-100"
	on:click={toggleMode}
	aria-label="Toggle Dark Mode"
> -->

<div class="relative aspect-square size-48 md:size-64">
	<svg
		width="0.87em"
		height="1em"
		class="absolute top-0 left-0 z-[1] aspect-square size-full drop-shadow-md"
		data-icon="skeleton-full"
	>
		<symbol id="ai:local:skeleton-full" viewBox="0 0 104 120"
			><path
				fill="currentColor"
				fill-rule="evenodd"
				d="M51.486 30.632c15.041 0 27.881 5.208 37.07 13.764q1.839-.568 3.118-1.165 3.91-1.822 10.265-7.474-1.032 9.844-2.216 14.454-.634 2.47-1.964 5.978a48 48 0 0 1 5.062 14.02c3.625 18.17-2.583 26.205-16.988 30.69q.162.81.253 1.741c.515 5.315-2.173 13.899-5.4 13.899-2.122 0-3.502-2.956-4.982-7.27-.463 4.996-2.74 10.731-5.379 10.731-2.222 0-3.63-3.24-5.192-7.889l-.22-.657c-.366-1.11-.744-2.292-1.145-3.522q-.15.712-.314 1.452l-.134.593C62.208 114.832 60.594 120 57.987 120c-3.027 0-1.367-9.529-6.47-13.567-25.873 1.41-47.638-8.154-47.638-35.75 0-5.078 1.202-10.039 3.405-14.643a56 56 0 0 1-1.1-2.28Q3.608 48.074 0 36.55q9.23 6.885 14.157 8.204.493.132 1.027.23c8.617-8.633 21.5-14.352 36.302-14.352M45.45 67.86c-8.742 0-15.829 6.882-15.829 15.372s7.087 15.372 15.829 15.372c8.741 0 15.828-6.882 15.828-15.372 0-8.386-6.915-15.204-15.51-15.37Zm22.466 19.2c-2.645 0-4.04 5.648-4.04 8.182 0 1.979.664 3.071 1.746 3.27 2.626.244 1.825-3.583 3.132-3.583 1.394 0 3.026 3.915 4.392 3.384 1.622-.966 1.002-3.07.442-4.734-1.973-4.18-3.027-6.519-5.672-6.519m-22.1-16.981c7.549 0 13.668 5.862 13.668 13.093s-6.12 13.093-13.668 13.093q-.696 0-1.376-.066c6.903-.66 12.292-6.241 12.292-13.027s-5.39-12.367-12.291-13.028q.678-.065 1.375-.065m39.31-3.12c-6.024 0-10.907 6.044-10.907 13.5S79.102 93.96 85.126 93.96s10.907-6.045 10.907-13.5c0-7.457-4.883-13.502-10.907-13.502Zm.352 2.228c5.03 0 9.107 5.039 9.107 11.255 0 6.215-4.077 11.254-9.107 11.254a7.5 7.5 0 0 1-1.368-.126c4.38-.816 7.739-5.487 7.739-11.128S88.49 70.129 84.11 69.313a7.5 7.5 0 0 1 1.368-.126m2.094-43.29c.35.227.448.694.222 1.044l-1.792 2.76a.754.754 0 0 1-.945.276l-.098-.054a.754.754 0 0 1-.222-1.043l1.792-2.76a.754.754 0 0 1 1.043-.223m-71.6-.728.07.087 1.652 2.36a.754.754 0 0 1-1.235.865l-1.652-2.36a.754.754 0 0 1 1.165-.952M53.954 0l1.709 14.476 6.371-7.161.165 17.826-1.263-.28q-5.526-1.226-10.024-1.225-4.463 0-8.372 1.204l-1.404.433 1.15-22.296 5.828 11.018zm-1.123 8.03-4.498 10.778-4.378-8.277-.617 11.951.131-.032q3.45-.85 7.253-.87l.19-.001q4.121 0 9 .965l.207.041-.092-9.92-5.87 6.597zm23.312 11.54a.755.755 0 0 1 .518.933l-.227.791a.754.754 0 0 1-.832.539l-.1-.022a.754.754 0 0 1-.518-.933l.227-.791a.754.754 0 0 1 .932-.517m-45.549-.85.424 1.306a.754.754 0 0 1-1.396.561l-.038-.095-.424-1.305a.754.754 0 0 1 1.434-.466Z"
			></path></symbol
		><use href="#ai:local:skeleton-full"></use>
	</svg>
	<div
		class="from-success-500 to-primary-500 absolute top-0 left-0 aspect-square size-full rounded-full bg-conic blur-3xl"
	></div>
</div>


<button
	type="button"
	class="btn-icon preset-filled rounded-full active:scale-[0.98] active:transition-all"
	on:click={toggleMode}
	aria-label="Toggle Dark Mode"
>
	{#if isDark}
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="20"
			height="20"
			viewBox="0 0 24 24"
			fill="none"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
			class="lucide lucide-sun-icon lucide-sun"
			><circle cx="12" cy="12" r="4" /><path d="M12 2v2" /><path d="M12 20v2" /><path
				d="m4.93 4.93 1.41 1.41"
			/><path d="m17.66 17.66 1.41 1.41" /><path d="M2 12h2" /><path d="M20 12h2" /><path
				d="m6.34 17.66-1.41 1.41"
			/><path d="m19.07 4.93-1.41 1.41" /></svg
		>
	{:else}
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="20"
			height="20"
			viewBox="0 0 24 24"
			fill="none"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
			class="lucide lucide-moon-icon lucide-moon"
			><path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z" /></svg
		>
	{/if}
</button>
