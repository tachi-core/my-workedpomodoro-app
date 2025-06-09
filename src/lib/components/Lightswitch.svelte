<script lang="ts">
	let darkMode = false;
	function toggle() {
		darkMode = !darkMode;
		window.document.body.classList.toggle('dark');
	}

	// function toggleDarkMode() {
	// 	document.documentElement.classList.toggle('dark');
	// }
</script>

<!-- <button on:click={toggle}>
	{#if darkMode}
		Go light
	{:else}
		Go dark
	{/if}
</button> -->

<button
	type="button"
	class="flex h-8 w-8 cursor-pointer items-center justify-center rounded-full bg-slate-300 text-slate-800 dark:bg-slate-700 dark:text-slate-100 active:scale-[0.98] active:transition-all"
	on:click={toggle}
	aria-label="true"
>
	{#if darkMode}
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
	<!-- <svg
					xmlns="http://www.w3.org/2000/svg"
					width="18"
					height="18"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="lucide lucide-sun-moon-icon lucide-sun-moon"
					><path d="M12 8a2.83 2.83 0 0 0 4 4 4 4 0 1 1-4-4" /><path d="M12 2v2" /><path
						d="M12 20v2"
					/><path d="m4.9 4.9 1.4 1.4" /><path d="m17.7 17.7 1.4 1.4" /><path d="M2 12h2" /><path
						d="M20 12h2"
					/><path d="m6.3 17.7-1.4 1.4" /><path d="m19.1 4.9-1.4 1.4" /></svg
				> -->
</button>
