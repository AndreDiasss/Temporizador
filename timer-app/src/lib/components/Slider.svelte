<script lang="ts">
  export let open: boolean = false; // controla se o slider está aberto
  export let width: number = 250;   // largura da sidebar em px
  export let closeOnOverlay: boolean = true;

  function close() {
    open = false;
  }
</script>

{#if open}
  <!-- Overlay -->
  <div class="overlay" on:click={() => closeOnOverlay && close()}></div>
{/if}

<!-- Sidebar -->
<div class="slider {open ? 'open' : ''}" style="width:{width}px">
  <slot></slot>
</div>

<style>
  .overlay {
    position: fixed;
    top:0; left:0;
    width:100vw;
    height:100vh;
    background: rgba(0,0,0,0.4);
    z-index:999;
  }

  .slider {
    position: fixed;
    top:0; left:0;
    height: 100vh;
    background: #e0daec;
    transform: translateX(-100%);
    transition: transform 0.3s ease;
    z-index:1000;
    padding:1rem;
    overflow-y:auto;
    display:flex;
    flex-direction:column;
  }

  .slider.open {
    transform: translateX(0);
  }
</style>