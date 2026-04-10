<script lang="ts">
  import Slider from '$lib/components/Slider.svelte';
  import Timer from '$lib/components/Timer.svelte';
  import { onMount } from 'svelte';

  interface TimerConfig {
    id: number;
    name: string;
    seconds: number;
    time: string;
    color: string;
  }
  
  let sidebarOpen: boolean = false;
  let timers: TimerConfig[] = [];
  let numTimers: number = 2;
  const colors = ["#6a4cff","#2a4cff","#B892FF","#E992FF"];
  let globalTime = 0;
  let globalRunning = false;
  let globalInterval: number;
  let resetAll = 0;

  function resetTimers() {
    resetAll++;
    globalTime = 0;
    globalRunning = false;
    clearInterval(globalInterval);
  }
  
  function initTimers() {
    timers = [];
    for (let i = 0; i < numTimers; i++) {
      timers.push({
        id: i + 1,
        name: `Timer ${i + 1}`,
        seconds: 30,
        time: "60",
        color: colors[i % colors.length]
      });
    }
  }

  function onClick() {
    if (sidebarOpen) {
      sidebarOpen = false;
    }else{
      sidebarOpen = true;
    }
  }

  function selectTimers(number: number) {
    numTimers = number;
    initTimers();
  }

  function updateTime(timer: TimerConfig) {
    const seconds = parseInt(timer.time);

    if (isNaN(seconds) || seconds < 0) return;

    timer.seconds = seconds;

    // 👇 força reatividade
    timers = [...timers];
  }



function toggleGlobalTimer() {
  if (!globalRunning) {
    globalRunning = true;

    globalInterval = window.setInterval(() => {
      globalTime++;
    }, 1000);

  } else {
    globalRunning = false;
    clearInterval(globalInterval);
  }
}

function formatGlobalTime(seconds: number) {
  const m = Math.floor(seconds / 60).toString().padStart(2, '0');
  const s = (seconds % 60).toString().padStart(2, '0');
  return `${m}:${s}`;
}

  onMount(() => {
    initTimers();
  });
</script>

<!-- Botão para abrir sidebar -->
<button
  class="menu-btn"
  on:click={() => onClick()}
  aria-label="Abrir menu de configurações"
>
  ☰
</button>

<div class="global-container">
  <button class="global-circle" on:click={toggleGlobalTimer}>
    {formatGlobalTime(globalTime)}
  </button>

  <button class="reset-all" on:click={resetTimers}>
    Reset
  </button>
</div>

<!-- Sidebar -->
<Slider bind:open={sidebarOpen}>
  <h2 class="sidebar-title">Settings</h2>
  <div class="timer-selector">
    {#each [1,2,3,4] as number}
      <button
        class="timer-block {numTimers === number ? 'active' : ''}"
        on:click={() => selectTimers(number)}
      >
        {number}
      </button>
    {/each}
  </div>

  {#each timers as timer}
    <div class="timer-config">
      <label class="inline-label">
        Name:
        <input type="text" bind:value={timer.name} />
      </label>
      <label class="inline-label">
        Time (seconds):
        <input
          type="number"
          min="0"
          inputmode="numeric"
          bind:value={timer.time}
          on:input={() => updateTime(timer)}
          placeholder="30"
        />
      </label>
    </div>
  {/each}
</Slider>

<!-- Área principal com timers -->
<div class="timers-container">
  {#each timers as timer}
    <div class="timer-wrapper">
      <Timer
        id={timer.id.toString()}
        defaultSeconds={timer.seconds}
        color={timer.color}
        resetSignal={resetAll}
      />
      <div class="timer-name">{timer.name}</div>
    </div>
  {/each}
</div>

<style>
  .menu-btn {
    position: fixed;
    top: 1rem;
    left: 1rem;
    z-index: 1100;
    background: #6a1aac;
    color: #fff;
    border: none;
    border-radius: 13px;
    font-size: 1.5rem;
    padding: 0.3rem 0.6rem;
    border: black 2px solid;
  }

  .timer-config {
    margin-top: 1rem;
    border: #430caa 2px solid;
    border-radius: 8px;
    padding: 0.5rem;
    gap: 1rem;
  }

  .timer-config label {
    display: flex;
    flex-direction: column;
    font-size: 0.9rem;
    margin-bottom: 0.5rem;
    color: #430caa;
  }

  .timer-config input[type="text"] {
    width: 100px;
    border: #430caa 1px solid;
    border-radius: 4px;
    padding: 0.3rem;
    font-size: 1rem;
  }

  .inline-label {
    display: flex;
    flex-direction: column;
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
    color: #430caa;
  }

  .timer-config .inline-label {
    display: flex;
    flex-direction: column;
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
    color: #430caa;
  }

  .inline-label input {
    margin-top: 0.2rem;
    width: 95%;
    border: #430caa 1px solid;
    border-radius: 4px;
    padding: 0.2rem;
  }

  .timers-container {
    display: grid;
    grid-template-columns: repeat(2, 200px);
    justify-content: center;
    gap: 2rem;
    padding: 2rem;
  }

  .timer-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    border: #8226fa 2px solid;
    border-radius: 20px;
    padding: 1rem;
  }

  .timer-wrapper:last-child:nth-child(odd) {
    grid-column: span 2;
    justify-self: center;
  }

  .timer-name {
    margin-top: 0.3rem;
    font-weight: bold;
    text-align: center;
    color:rgba(255, 255, 255, 0.705);
  }
  
  .sidebar-title{
    margin-top: 0.2rem;
    margin-bottom: 0.9rem;
    margin-left: 5rem;
    font-size: 1.9rem;
    color: #591591;
    font-weight: bold;
    font-style:inherit ;
  }

  .timer-selector {
    display: flex;
    gap: 0.8rem;
    margin-bottom: 1rem;
  }

  .timer-block {
    width: 45px;
    height: 45px;
    border-radius: 10px;
    border: 2px solid #6a1aac;
    background: white;
    color: #33146b;
    font-weight: bold;
    font-size: 1.2rem;
    cursor: pointer;
    transition: all 0.2s ease;
    margin-top: 0.5rem;
  }

  .timer-block:hover {
    background: #430caa;
    color: white;
  }

  .timer-block.active {
    background: #6a1aac;
    color: white;
  }

.global-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin-top: 2rem;
}

.reset-all {
  height: 40px;
  padding: 0 15px;
  border-radius: 8px;
  border: none;
  background: #220753;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

.global-circle {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  font-size: 2rem;
  font-weight: bold;
  border: 4px solid #430caa;
  background: white;
  cursor: pointer;
}

</style>