<script lang="ts">
  import { P5, P5Sketch } from "@netalondon/svelte-p5";
  import type { Vector } from "p5";

  class Sketch extends P5Sketch {
    private res = 15;
    private minDuration = 800;
    private maxDuration = 1200;
    private minChange = 0.1;
    private maxChange = 0.3;

    private params = [0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 1, 1, 1, 0, 0.3, 0.6];
    private transitionIndex = 0;
    private transitionStart = 0;
    private transitionEnd = 0;
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
      let a = this.p5.createVector(
        this.params[0],
        this.params[1],
        this.params[2]
      );

      let b = this.p5.createVector(
        this.params[3],
        this.params[4],
        this.params[5]
      );

      let c = this.p5.createVector(
        this.params[6],
        this.params[7],
        this.params[8]
      );

      let d = this.p5.createVector(
        this.params[9],
        this.params[10],
        this.params[11]
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
      this.transitionIndex = Math.floor(this.p5.random(0, 12));
      this.transitionStart = this.params[this.transitionIndex];
      let step = this.p5.random(this.minChange, this.maxChange);
      if (this.p5.random() < 0.5) {
        step = -step;
      }
      this.transitionEnd = this.transitionStart + step;
      this.transitionProgress = 0;
      this.transitionDuration = this.p5.random(
        this.minDuration,
        this.maxDuration
      );
    }

    private didEndTransition() {
      return this.transitionProgress >= this.transitionDuration;
    }

    private applyTransition() {
      this.params[this.transitionIndex] = this.p5.lerp(
        this.transitionStart,
        this.transitionEnd,
        this.transitionProgress / this.transitionDuration
      );
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
