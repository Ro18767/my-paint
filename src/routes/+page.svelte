<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;
  let canvasWidth = 500;
  let canvasHeight = 500;

  let ctx: CanvasRenderingContext2D;

  let color = "#000000";
  let width = 5;

  onMount(() => {
    ctx = canvas.getContext("2d")!;
  });

  $: if (ctx) ctx.lineCap = "round";
  // $: if (ctx) ctx.lineJoin = "round";
  $: if (ctx) ctx.lineWidth = width || 1;
  $: if (ctx) ctx.strokeStyle = color;

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

<input type="color" bind:value={color} />
<input type="range" min="1" max="100" bind:value={width} />
<button on:click={reset}>clear canvas</button>
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

<style>
  canvas {
    width: 500px;
    height: 500px;
    border: 2px solid;

    /* image-rendering: pixelated; */
    /* image-rendering: crisp-edges; */
  }
</style>
