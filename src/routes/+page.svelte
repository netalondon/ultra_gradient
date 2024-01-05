<script lang="ts">
  import { Sketch } from "$lib/Sketch";
  import { P5 } from "@netalondon/svelte-p5";  

  const sketch = new Sketch();
</script>

<div class="root">
  <P5 {sketch}></P5>
</div>

<style>
  .root {
    width: 100vw;
    height: 100vh;
  }

  :global(body) {
    margin: 0;
  }
</style>
