<script lang="ts">
	import Lightswitch from '$lib/components/Lightswitch.svelte';
	import { onDestroy } from 'svelte';
	import { Progress } from '@skeletonlabs/skeleton-svelte';
	import IconVolumeOn from '@lucide/svelte/icons/volume-2';
	import IconVolumeOff from '@lucide/svelte/icons/volume-off';
	import IconStopCircle from '@lucide/svelte/icons/stop-circle';
	import { Popover } from '@skeletonlabs/skeleton-svelte';
	import IconInfo from '@lucide/svelte/icons/info';
	import IconX from '@lucide/svelte/icons/x';

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
	let isPaused = false;
	let isMuted = false;
	let alarmAudio: HTMLAudioElement | null = null;
	let openInfo = false;
	let progress = 0;

	// Supaya timer jalan saat tidak buka tab browser di HP
	let startTime: number | null = null;
	let expectedEndTime: number | null = null;

	function startOrPauseTimer() {
		if (isRunning && !isPaused) {
			isPaused = true;
			pauseTimer();
		} else {
			isPaused = false;
			isRunning = true;
			if (!startTime) {
				startTime = Date.now();
				expectedEndTime = startTime + timeLeft * 1000;
			} else {
				// lanjutkan dari pause
				expectedEndTime = Date.now() + timeLeft * 1000;
			}
			timer = setInterval(updateTime, 1000);
			stopAlarm();
		}
	}

	function updateTime() {
		if (!expectedEndTime) return;

		const now = Date.now();
		const diff = Math.max(0, Math.floor((expectedEndTime - now) / 1000));
		timeLeft = diff;
		updateProgress();

		if (diff <= 0) {
			stopTimer();
			playAlarm();
		}
	}

	function stopTimer() {
		if (timer) clearInterval(timer);
		timer = null;
		isRunning = false;
		isPaused = false;
		startTime = null;
		expectedEndTime = null;
	}

																


	function updateProgress() {
		progress = ((totalTime - timeLeft) / totalTime) * 100;
	}

	function formatTime(seconds: number) {
		const m = Math.floor(seconds / 60);
		const s = seconds % 60;
		return `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
	}

	// function updateTime() {
	// 	if (timeLeft > 0) {
	// 		timeLeft--;
	// 		updateProgress();
	// 	} else {
	// 		stopTimer();
	// 		playAlarm();
	// 	}
	// }

	// function startOrPauseTimer() {
	// 	if (isRunning && !isPaused) {
	// 		// sedang berjalan, lalu dipause
	// 		isPaused = true;
	// 		pauseTimer();
	// 	} else {
	// 		// mulai dari pause
	// 		isPaused = false;
	// 		isRunning = true;
	// 		timer = setInterval(updateTime, 1000);
	// 		stopAlarm();
	// 	}
	// }

	function pauseTimer() {
		if (timer) clearInterval(timer);
		isRunning = false;
	}

	// function stopTimer() {
	// 	if (timer) clearInterval(timer);
	// 	timer = null;
	// 	isRunning = false;
	// 	isPaused = false;
	// }

	function resetTimer() {
		stopTimer();
		isPaused = false;
		timeLeft = durations[mode];
		totalTime = durations[mode];
		updateProgress();
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

	function closeInfoPopover() {
		openInfo = false;
	}
</script>

<svelte:head>
	<title>Pomodoro App</title>
</svelte:head>

<main
	class="bg-surface-50-950 flex min-h-screen flex-col items-center justify-center overflow-hidden text-center"
>
	<div class="relative mx-auto w-full max-w-md px-4">
		<div
			class="pointer-events-none absolute top-0 right-0 h-full w-full translate-x-1/3 -translate-y-1/3 animate-[slow-orbit_20s_linear_infinite] rounded-full blur-3xl"
		>
			<div
				class="from-primary-900 to-primary-600 h-full w-full rounded-full bg-conic opacity-0 transition-opacity duration-700 ease-in-out dark:opacity-80"
			></div>
			<div
				class="from-warning-500 to-warning-100 absolute top-0 left-0 h-full w-full rounded-full bg-conic opacity-80 transition-opacity duration-700 ease-in-out dark:opacity-0"
			></div>
		</div>

		<div
			class="card preset-outlined-surface-300-700 bg-surface-100-900/90 relative space-y-5 rounded-2xl p-5 shadow-xl"
		>
			<header class="flex justify-between">
				<div>
					<div class="flex items-center">
						<h1 class="mb-1 text-left text-2xl font-medium sm:text-3xl">Pomodoro Timer</h1>
						<Popover
							open={openInfo}
							onOpenChange={(e) => (openInfo = e.open)}
							positioning={{ placement: 'top' }}
							triggerBase="btn-icon hover:preset-tonal rounded-full p-1 m-1"
							contentBase="card bg-surface-200-800 p-4 space-y-1 max-w-[320px] text-sm rounded-lg preset-outlined-surface-300-700"
							arrow
							arrowBackground="!bg-surface-200 dark:!bg-surface-800"
						>
							{#snippet trigger()}<IconInfo class="h-5 w-5" />{/snippet}
							{#snippet content()}
								<header class="flex items-start justify-between">
									<p class="font-bold">Apa itu Pomodoro Timer?</p>
									<button class="btn-icon hover:preset-tonal" on:click={closeInfoPopover}>
										<IconX class="h-4 w-4" />
									</button>
								</header>
								<article class="opacity-90">
									Teknik manajemen waktu yang membagi pekerjaan menjadi sesi Fokus 25 menit,
									istirahat 5 menit, lalu istirahat panjang 15 menit setiap 4 sesi.
								</article>
							{/snippet}
						</Popover>
					</div>
					<p class="text-left text-xs opacity-60">{dateWithOrdinal}</p>
				</div>
				<Lightswitch />
			</header>

			<hr class="hr border-surface-950-50" />

			<nav
				class="btn-group preset-outlined-surface-950-50 w-full flex-row justify-center rounded-lg p-1.5 sm:justify-between sm:rounded-xl sm:p-2"
			>
				{#each modes as m}
					<button
						class={`btn h-10 max-w-full min-w-[5rem] flex-1 rounded-md text-xs font-semibold transition-all active:scale-[0.98] sm:text-sm
							${mode === m ? 'preset-filled' : 'hover:preset-tonal'}
							${isRunning || isPaused ? 'cursor-not-allowed opacity-60' : ''}`}
						disabled={isRunning || isPaused}
						on:click={() => switchMode(m)}
					>
						{m === 'pomodoro' ? 'Pomodoro' : m === 'shortBreak' ? 'Short Break' : 'Long Break'}
					</button>
				{/each}
			</nav>

			<div class="mb-4 w-full max-w-md">
				<Progress value={progress} meterBg="bg-surface-950-50" trackBg="bg-surface-300-700" />
			</div>

			<div id="timer-display" class="my-6 font-mono text-6xl">{formatTime(timeLeft)}</div>

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

			<div class="mt-3 flex justify-center gap-2">
				<button class="btn text-xs" on:click={() => (isMuted = !isMuted)}>
					{#if isMuted}
						<IconVolumeOff size="16" /> Unmute
					{:else}
						<IconVolumeOn size="16" /> Mute
					{/if}
				</button>
				{#if alarmAudio}
					<button class="btn text-xs" on:click={stopAlarm}>
						<IconStopCircle size="16" /> Stop Sound
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
