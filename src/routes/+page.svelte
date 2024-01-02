<script lang="ts">
  import { P5, P5Sketch } from "@netalondon/svelte-p5";
  import type { Vector } from "p5";

  function dist(start: number[], end: number[]) {
    let N = Math.min(start.length, end.length);
    let sum = 0;
    for (let i = 0; i < N; i++) {
      sum += Math.pow(end[i] - start[i], 2);
    }
    return Math.sqrt(sum) / Math.sqrt(N);
  }

  class Sketch extends P5Sketch {
    private res = 15;
    private minDuration = 20000;
    private maxDuration = 21000;

    private state = [0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 1, 1, 1, 0, 0.3, 0.6];
    private start = Array.from(this.state);
    private end = Array.from(this.state);

    private transitionProgress = 0;
    private transitionDuration = 0;

    public setup() {
      this.createCanvas();
      this.p5.noStroke();
      this.p5.background(200, 20, 60);
    }

    private size() {
      return this.getWidth() / this.res;
    }

    private cos(v: Vector) {
      return this.p5.createVector(Math.cos(v.x), Math.cos(v.y), Math.cos(v.z));
    }

    private multElementWise(v1: Vector, v2: Vector) {
      return this.p5.createVector(v1.x * v2.x, v1.y * v2.y, v1.z * v2.z);
    }

    private getColor(u: number) {
      let a = this.p5.createVector(this.state[0], this.state[1], this.state[2]);

      let b = this.p5.createVector(this.state[3], this.state[4], this.state[5]);

      let c = this.p5.createVector(this.state[6], this.state[7], this.state[8]);

      let d = this.p5.createVector(
        this.state[9],
        this.state[10],
        this.state[11]
      );

      let color = a.add(
        this.multElementWise(
          b,
          this.cos(
            c
              .mult(u)
              .add(d)
              .mult(Math.PI * 2)
          )
        )
      );
      return this.p5.color(color.x * 255, color.y * 255, color.z * 255);
    }

    private newTransition() {
      this.start = Array.from(this.state);
      this.end = Array.from(this.state);
      for (let i = 0; i < this.state.length; i++) {
        this.end[i] = this.p5.random();
      }
      this.transitionProgress = 0;
      this.transitionDuration =
        this.p5.random(this.minDuration, this.maxDuration) *
        dist(this.start, this.end);
    }

    private didEndTransition() {
      return this.transitionProgress >= this.transitionDuration;
    }

    private applyTransition() {
      for (let i = 0; i < this.state.length; i++) {
        this.state[i] = this.p5.lerp(
          this.start[i],
          this.end[i],
          this.transitionProgress / this.transitionDuration
        );
      }
    }

    private update() {
      if (this.didEndTransition()) {
        this.newTransition();
      }
      this.applyTransition();
      this.transitionProgress += this.p5.deltaTime;
    }

    public draw(): void {
      this.update();
      for (let i = 0; i < this.res; i++) {
        this.p5.fill(this.getColor(i / this.res));
        this.p5.rect(i * this.size(), 0, this.size(), this.getHeight());
      }
      // this.p5.noLoop();
    }
  }

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
