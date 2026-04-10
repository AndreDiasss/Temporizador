<script lang="ts">
  import { onDestroy } from 'svelte';

  export let id: string;
  export let defaultMinutes: number = 1;
  export let defaultSeconds: number = 0; 
  export let color: string = '#4caf50';
  export let resetSignal: number;
  let running: boolean = false;
  let startTimestamp: number = 0;
  let interval: number;

  let totalTime = defaultMinutes * 60 + defaultSeconds;

  let timeLeft: number = totalTime;
  let remainingBeforePause: number = totalTime;

  $: if (resetSignal) {
    resetTimer();
  }

  $: {
    const newTotal = defaultMinutes * 60 + defaultSeconds;

    if (newTotal !== totalTime && !running) {
      totalTime = newTotal;
      timeLeft = newTotal;
      remainingBeforePause = newTotal;
    }
  }


  const formatTime = (seconds: number): string => {
    const m = Math.floor(seconds / 60).toString().padStart(2, '0');
    const s = (seconds % 60).toString().padStart(2, '0');
    return `${m}:${s}`;
  };

  function toggleTimer(): void {
    if (!running) {
      running = true;
      startTimestamp = Date.now();

      interval = window.setInterval(() => {
        const elapsed = Math.floor((Date.now() - startTimestamp) / 1000);
        timeLeft = remainingBeforePause - elapsed;

        if (timeLeft <= 0) {
          timeLeft = 0;
          running = false;
          clearInterval(interval);
          remainingBeforePause = 0;

          const audio = new Audio('/alarm.mp3');
          audio.play();
          if (navigator.vibrate) navigator.vibrate(1000);
        }
      }, 200);

    } else {
      running = false;
      clearInterval(interval);
      remainingBeforePause = timeLeft;
    }
  }

  function resetTimer(): void {
    running = false;
    clearInterval(interval);
    timeLeft = totalTime;
    remainingBeforePause = totalTime;
  }

  onDestroy(() => {
    clearInterval(interval);
  });

  $: progress =
    totalTime > 0
      ? ((totalTime - timeLeft) / totalTime) * 100
      : 0;
</script>



<div class="timer">
  <button
    class="circle"
    style="background: conic-gradient({color} {progress}%, #ddd {progress}% 100%);"
    on:click={toggleTimer}
    on:contextmenu|preventDefault={resetTimer}
    aria-label={`Timer ${id}, ${formatTime(timeLeft)} minutos`}
  >
    <span class="time">{formatTime(timeLeft)}</span>
  </button>
</div>



<style>
  .timer {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 1rem;
    
  }

  button.circle {
    width: 120px;
    height: 120px;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: background 0.2s linear;
    margin-bottom: 0.5rem;
    position: relative;
    touch-action: manipulation;
    border: none;
    outline: none;
  }

  .time {
    font-size: 1.5rem;
    font-weight: bold;
    position: absolute;
    user-select: none;
  }
</style>