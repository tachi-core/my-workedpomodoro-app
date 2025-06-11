<script lang="ts">
	import Lightswitch from '$lib/components/Lightswitch.svelte';
	import { onDestroy } from 'svelte';
	import { Progress } from '@skeletonlabs/skeleton-svelte';
	import IconVolumeOn from '@lucide/svelte/icons/volume-2';
	import IconVolumeOff from '@lucide/svelte/icons/volume-off';
	import IconStopCircle from '@lucide/svelte/icons/stop-circle';


	const durations = {
		pomodoro: 25 * 60,
		shortBreak: 5 * 60,
		longBreak: 15 * 60
	};

	const modes = ['pomodoro', 'shortBreak', 'longBreak'] as const;
	type Mode = (typeof modes)[number];

	let mode: Mode = 'pomodoro';
	let timeLeft = durations[mode];
	let totalTime = durations[mode];
	let timer: ReturnType<typeof setInterval> | null = null;
	let isRunning = false;
	let isMuted = false;
	let alarmAudio: HTMLAudioElement | null = null;

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
		if (isRunning) {
			pauseTimer();
		} else {
			isRunning = true;
			timer = setInterval(updateTime, 1000);
			stopAlarm();
		}
	}

	function pauseTimer() {
		if (timer) clearInterval(timer);
		isRunning = false;
	}

	function stopTimer() {
		if (timer) clearInterval(timer);
		timer = null;
		isRunning = false;
	}

	function resetTimer() {
		stopTimer();
		timeLeft = durations[mode];
		totalTime = durations[mode];
		stopAlarm();
	}

	function switchMode(m: Mode) {
		mode = m;
		resetTimer();
	}

	function playAlarm() {
		if (!isMuted) {
			alarmAudio = new Audio('/alarm.mp3');
			alarmAudio.play();
		}
	}

	function stopAlarm() {
		if (alarmAudio) {
			alarmAudio.pause();
			alarmAudio.currentTime = 0;
			alarmAudio = null;
			resetTimer();
		}
	}

	$: progress = ((totalTime - timeLeft) / totalTime) * 100;

	onDestroy(() => stopTimer());

	const now = new Date();
	const day = now.getDate();
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
	const dateWithOrdinal = formattedDate.replace(/\d+/, `${day}${getOrdinal(day)}`);
</script>

<svelte:head>
	<title>Pomodoro App</title>
</svelte:head>

<main class="bg-surface-50-950 flex min-h-screen flex-col items-center justify-center p-6 text-center overflow-hidden">
	<div class="relative mx-auto max-w-[120vw] sm:max-w-md">
		<!-- Efek Cahaya -->
		<div class="pointer-events-none absolute top-0 right-0 h-full w-full translate-x-1/3 -translate-y-1/3 animate-[slow-orbit_20s_linear_infinite] rounded-full blur-3xl">
			<!-- Light mode -->
			<div class="bg-conic from-primary-900 to-primary-600 h-full w-full rounded-full opacity-0 transition-opacity duration-700 ease-in-out dark:opacity-80"></div>
			<!-- Dark mode -->
			<div class="bg-conic from-warning-500 to-warning-100 absolute top-0 left-0 h-full w-full rounded-full opacity-80 transition-opacity duration-700 ease-in-out dark:opacity-0"></div>
		</div>

		<!-- Card -->
		<div class="card preset-outlined-surface-300-700 bg-surface-100-900/90 relative z-10 space-y-5 rounded-2xl p-5 shadow-xl">
			<header class="flex justify-between">
				<div>
					<h1 class="mb-1 text-left text-3xl font-medium">Pomodoro Timer</h1>
					<p class="text-left text-xs opacity-60">{dateWithOrdinal}</p>
				</div>
				<Lightswitch />
			</header>

			<hr class="hr" />

			<!-- Mode Buttons -->
			<nav class="btn-group preset-outlined-surface-300-700 flex-row rounded-xl p-2">
				{#each modes as m}
					<button
						class={`btn h-10 rounded-md text-xs font-semibold transition-all active:scale-[0.98] sm:text-sm
							${mode === m ? 'preset-filled' : 'hover:preset-tonal'}
							${isRunning ? 'opacity-50 cursor-not-allowed' : ''}`}
						disabled={isRunning}
						on:click={() => switchMode(m)}
					>
						{m === 'pomodoro' ? 'Pomodoro' : m === 'shortBreak' ? 'Short Break' : 'Long Break'}
					</button>
				{/each}
			</nav>

			<!-- Progress bar -->
			<div class="mb-4 w-full max-w-md">
				<Progress value={progress} meterBg="bg-surface-950-50" />
			</div>

			<!-- Timer display -->
			<div id="timer-display" class="my-6 font-mono text-6xl">{formatTime(timeLeft)}</div>

			<!-- Control buttons -->
			<div class="space-x-2">
				<button
					class="btn preset-filled h-10 min-w-[100px] rounded-lg font-semibold active:scale-[0.98]"
					on:click={startOrPauseTimer}
				>
					{isRunning ? 'Pause' : 'Start'}
				</button>
				<button
					class="btn preset-outlined-surface-800-200 hover:preset-tonal h-10 min-w-[100px] rounded-lg font-semibold active:scale-[0.98]"
					on:click={resetTimer}
				>
					Reset
				</button>
			</div>

			<!-- Sound control -->
			<div class="mt-3 flex justify-center gap-2">
				<button class="btn text-xs" on:click={() => (isMuted = !isMuted)}>
					{#if isMuted}
					<IconVolumeOff size="16" />
					Unmute
					{:else}
					<IconVolumeOn size="16" />
					Mute
					{/if}
				</button>
				{#if alarmAudio}
					<button class="btn text-xs" on:click={stopAlarm}>
						<IconStopCircle size="16" />
						Stop Sound
					</button>
				{/if}
			</div>
		</div>
	</div>
</main>

<style>
	button:hover {
		opacity: 0.9;
	}
</style>
