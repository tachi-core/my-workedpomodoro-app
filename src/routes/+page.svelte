<script lang="ts">
	import Lightswitch from '../lib/components/Lightswitch.svelte';
	import { onDestroy } from 'svelte';
	import { tick } from 'svelte';

	const durations = {
		pomodoro: 25 * 60,
		shortBreak: 5 * 60,
		longBreak: 15 * 60
	};

	const modes = ['pomodoro', 'shortBreak', 'longBreak'] as const;

	type Mode = (typeof modes)[number]; // "pomodoro" | "shortBreak" | "longBreak"

	let mode: Mode = 'pomodoro';

	let timeLeft = durations[mode];
	let totalTime = durations[mode];
	let timer: ReturnType<typeof setInterval> | null = null;
	let isRunning = false;
	// let isPaused = false;

	function formatTime(seconds: number) {
		const m = Math.floor(seconds / 60);
		const s = seconds % 60;
		return `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
	}

	function updateTime() {
		if (timeLeft > 0) {
			timeLeft--;
		} else {
			stopTimer();
			playAlarm();
		}
	}

	function startOrPauseTimer() {
		// if (!isRunning && !isPaused) {
		if (!isRunning) {
			isRunning = true;
			timer = setInterval(() => {
				updateTime();
			}, 1000);
		} else {
			pauseTimer();
		}
		// else if (isPaused) {
		// 	resumeTimer();
		// }
	}

	function pauseTimer() {
		if (timer) clearInterval(timer);
		isRunning = false;
		// isPaused = true;
	}

	function stopTimer() {
		if (timer) clearInterval(timer);
		timer = null;
		isRunning = false;
		// isPaused = false;
	}

	function resetTimer() {
		stopTimer();
		timeLeft = durations[mode];
		totalTime = durations[mode];
	}

	function switchMode(m: Mode) {
		mode = m;
		resetTimer();
	}

	function playAlarm() {
		const audio = new Audio('/alarm.mp3');
		audio.play();
	}

	$: progress = ((totalTime - timeLeft) / totalTime) * 100;

	onDestroy(() => {
		stopTimer();
	});

	const now = new Date();

	const formattedDate = now.toLocaleDateString('en-US', {
		weekday: 'long',
		month: 'long',
		day: 'numeric'
	});

	function getOrdinal(n: number): string {
		const s = ['th', 'st', 'nd', 'rd'];
		const v = n % 100;
		return s[(v - 20) % 10] || s[v] || s[0];
	}

	const day = now.getDate();
	const dateWithOrdinal = formattedDate.replace(/\d+/, `${day}${getOrdinal(day)}`);

	// function toggleDarkMode() {
	// 	document.documentElement.classList.toggle('dark');
	// }
</script>

<svelte:head>
	<title>Pomodoro App</title>
	<!-- <meta name="description" content="This is where the description goes for SEO" /> -->
</svelte:head>

<main
	class="flex min-h-screen flex-col items-center justify-center bg-neutral-100 p-6 text-center dark:bg-neutral-900 dark:text-white"
>
	<div class="space-y-5 rounded-2xl p-5 outline-1 outline-slate-400 dark:outline-slate-600">
		<header class="flex justify-between">
			<div>
				<h1 class="mb-1 text-left text-3xl font-medium">Pomodoro Timer</h1>
				<p class="text-left text-xs opacity-60">{dateWithOrdinal}</p>
			</div>
			<Lightswitch />
		</header>

		<hr class="my-4 border-t border-slate-400 dark:border-slate-600" />

		<!-- Mode Buttons -->
		<div
			class="mb-4 flex flex-wrap gap-2 rounded-xl border border-slate-400 p-2 dark:border-slate-600"
		>
			{#each modes as m}
				<button
					class={`inline-flex h-9 cursor-pointer items-center justify-center rounded-md px-3 text-xs font-semibold transition-all active:scale-[0.98] sm:text-sm
					${
						mode === m
							? 'bg-slate-800 text-white dark:bg-slate-200 dark:text-slate-900'
							: 'text-slate-800 hover:bg-slate-800/10 dark:text-slate-200 dark:hover:bg-slate-200/10'
					}`}
					on:click={() => switchMode(m)}
				>
					{m === 'pomodoro' ? 'Pomodoro' : m === 'shortBreak' ? 'Short Break' : 'Long Break'}
				</button>
			{/each}
		</div>

		<!-- Progress bar -->
		<div class="mb-4 w-full max-w-md">
			<div class="h-2 overflow-hidden rounded-full bg-slate-400 dark:bg-slate-700">
				<div
					class="h-full bg-slate-800 transition-all duration-300 dark:bg-slate-200"
					style="width: {progress}%"
				></div>
			</div>
		</div>

		<div id="timer-display" class="my-6 font-mono text-6xl">{formatTime(timeLeft)}</div>

		<!-- Control buttons -->
		<div class="space-x-2">
			<button
				class="inline-flex h-10 min-w-[100px] cursor-pointer items-center justify-center rounded-lg bg-slate-800 px-[30px] text-[15px] font-semibold text-white active:scale-[0.98] active:transition-all dark:bg-slate-200 dark:text-slate-900"
				on:click={startOrPauseTimer}
			>
				{#if !isRunning}
					Start
				{:else}
					Pause
				{/if}
			</button>
			<button
				class="inline-flex h-10 min-w-[100px] cursor-pointer items-center justify-center rounded-lg border border-slate-400 px-[30px] text-[15px] font-semibold text-slate-800 hover:border-slate-800/15 hover:bg-slate-800/10 active:scale-[0.98] active:transition-all dark:border-slate-500 dark:text-white dark:hover:bg-slate-200/10"
				on:click={resetTimer}
			>
				Reset
			</button>
		</div>
	</div>
</main>

<style>
	button:hover {
		opacity: 0.9;
	}
</style>
