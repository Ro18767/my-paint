<script lang="ts">
  import { onMount } from "svelte";
  import { AppShell, AppBar } from "@skeletonlabs/skeleton";
  import { LightSwitch } from "@skeletonlabs/skeleton";

  let canvas: HTMLCanvasElement;
  let canvasWidth = 500;
  let canvasHeight = 500;

  let ctx: CanvasRenderingContext2D;

  let colorValue = "#000000";
  let width = 5;

  onMount(() => {
    ctx = canvas.getContext("2d")!;
  });

  $: if (ctx) ctx.lineCap = "round";
  // $: if (ctx) ctx.lineJoin = "round";
  $: if (ctx) ctx.lineWidth = width || 1;
  $: if (ctx) ctx.strokeStyle = colorValue;

  function reset() {
    if (ctx) ctx.clearRect(0, 0, canvasWidth, canvasHeight);
  }

  function startDrawLine(x: number, y: number) {
    ctx.beginPath();
    ctx.moveTo(x, y);
  }

  function endDrawLine(x: number, y: number) {
    ctx.lineTo(x, y);
    ctx.stroke();
    ctx.closePath();
  }
</script>

<AppShell
  slotPageContent="overflow-hidden"
  slotSidebarRight="bg-surface-800-100-token"
>
  <svelte:fragment slot="header">
    <AppBar
      gridColumns="grid-cols-3"
      slotDefault="text-base-token dark:text-dark-token"
      slotLead="text-base-token dark:text-dark-token place-content-start"
      slotTrail="text-base-token dark:text-dark-token place-content-end"
    >
      <svelte:fragment slot="lead">
        <button on:click={reset} class="btn variant-filled-primary">
          clear canvas
        </button>
      </svelte:fragment>
      (title)
      <svelte:fragment slot="trail"><LightSwitch /></svelte:fragment>
    </AppBar>
  </svelte:fragment>
  <svelte:fragment slot="sidebarRight">
    <div class="grid grid-cols-[auto_1fr] gap-2">
      <input class="input" type="color" bind:value={colorValue} />
      <input
        class="input"
        type="text"
        bind:value={colorValue}
        readonly
        tabindex="-1"
      />
    </div>
    <input type="range" min="1" max="100" bind:value={width} />
  </svelte:fragment>
  <AppShell
    slotPageContent="overflow-auto space-y-4 space-x-4"
    slotHeader="bg-none"
  >
    <svelte:fragment slot="header">
      <AppBar
        background="bg-surface-700-200-token"
        slotLead="text-dark-token dark:text-base-token place-content-start"
        slotTrail="text-dark-token dark:text-base-token place-content-end"
      >
        <svelte:fragment slot="lead">(icon)</svelte:fragment>
        <svelte:fragment slot="trail">(actions)</svelte:fragment>
      </AppBar>
    </svelte:fragment>

    <!-- Router Slot -->
    <canvas
      bind:this={canvas}
      width={canvasWidth}
      height={canvasHeight}
      on:pointerdown={(e) => {
        canvas.setPointerCapture(e.pointerId);
        startDrawLine(e.offsetX, e.offsetY);
      }}
      on:pointermove={(e) => {
        if (!canvas.hasPointerCapture(e.pointerId)) return;
        endDrawLine(e.offsetX, e.offsetY);
        startDrawLine(e.offsetX, e.offsetY);
      }}
      on:pointerup={(e) => {
        if (!canvas.hasPointerCapture(e.pointerId)) return;
        canvas.releasePointerCapture(e.pointerId);
        endDrawLine(e.offsetX, e.offsetY);
      }}
    />
    <!-- ---- / ---- -->
  </AppShell>
</AppShell>

<style>
  * {
    vertical-align: middle;
  }
  canvas {
    width: 500px;
    height: 500px;
    border: 2px solid;

    /* image-rendering: pixelated; */
    /* image-rendering: crisp-edges; */
  }
  /* canvas {
    width: 9999px;
    height: 9999px;
  } */
</style>
