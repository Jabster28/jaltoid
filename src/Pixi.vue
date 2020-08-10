<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png" />
    teeee -->
  </div>
</template>

<script lang="ts">
import * as PIXI from "pixi.js";

import Vue from "vue";

export default Vue.extend({
  mounted() {
    // listen for keys
    window.addEventListener("keydown", e => {
      e = e || window.event;
      switch (e.keyCode) {
        case 87:
          this.moving.up = true;
          break;
        case 65:
          this.moving.left = true;
          break;
        case 83:
          this.moving.down = true;
          break;
        case 68:
          this.moving.right = true;
          break;
      }
    });
    window.addEventListener("keyup", e => {
      e = e || window.event;
      switch (e.keyCode) {
        case 87:
          this.moving.up = false;
          break;
        case 65:
          this.moving.left = false;
          break;
        case 83:
          this.moving.down = false;
          break;
        case 68:
          this.moving.right = false;
          break;
      }
    });

    // start
    this.type = "WebGL";
    if (!PIXI.utils.isWebGLSupported()) {
      this.type = "canvas";
    }
    PIXI.utils.sayHello(this.type);

    // init
    const app = this.pixi.app;
    document.body.appendChild(app.view);
    this.pixi.app = app;
    const container = new PIXI.Container();
    app.stage.addChild(container);
    this.pixi.container = container;
    window.app = this;
    // sprite
    PIXI.Texture.fromURL("/square.png").then(texture => {
      const square = new PIXI.Sprite(texture);
      this.pixi.squareTexture = texture;
      this.pixi.square.sprite = square;
      square.anchor.set(0.5);
      square.x = 25;
      square.y = 25;
      container.addChild(square);

      // Move container to the center
      container.x = app.screen.width / 2;
      container.y = app.screen.height / 2;

      // Center square sprite in local container coordinates
      container.pivot.x = container.width / 2;
      container.pivot.y = container.height / 2;
    });
    app.ticker.add(delta => {
      if (this.moving.up && this.pixi.square.sprite.y == 240)
        this.pixi.square.velocity.y += 13;
      // if (this.moving.down)
      //   this.pixi.square.velocity.y -= this.config.acceleration;
      if (this.moving.right)
        this.pixi.square.velocity.x += this.config.acceleration;
      if (this.moving.left)
        this.pixi.square.velocity.x -= this.config.acceleration;
      // rotate the container!
      // use delta to create frame-independent transform
      // container.rotation -= 0.05 * delta;

      // slidiness
      if (this.pixi.square.velocity.y > 1) {
        this.pixi.square.velocity.y -= 0.6;
      } else {
        //   this.pixi.square.velocity.y += this.config.friction;
        // } else if (this.pixi.square.velocity.y != 0) {
        this.pixi.square.velocity.y = -11;
      }
      if (this.pixi.square.velocity.x > 1) {
        this.pixi.square.velocity.x -= this.config.friction;
      } else if (this.pixi.square.velocity.x < 0) {
        this.pixi.square.velocity.x += this.config.friction;
      } else if (this.pixi.square.velocity.x != 0) {
        this.pixi.square.velocity.x = 0;
      }

      // cap velocity at this.config.maxVel
      if (this.pixi.square.velocity.y > this.config.maxVel)
        this.pixi.square.velocity.y = this.config.maxVel;
      if (this.pixi.square.velocity.x > this.config.maxVel)
        this.pixi.square.velocity.x = this.config.maxVel;
      if (this.pixi.square.velocity.y < -this.config.maxVel)
        this.pixi.square.velocity.y = -this.config.maxVel;
      if (this.pixi.square.velocity.x < -this.config.maxVel)
        this.pixi.square.velocity.x = -this.config.maxVel;

      this.pixi.square.sprite.y -= this.pixi.square.velocity.y;
      this.pixi.square.sprite.x += this.pixi.square.velocity.x;

      if (this.pixi.square.sprite.x > 240) {
        this.pixi.square.sprite.x = 240;
        this.pixi.square.velocity.x = 0;
      }

      if (this.pixi.square.sprite.y < -190) {
        this.pixi.square.sprite.y = -190;
        this.pixi.square.velocity.y = 0;
      }

      if (this.pixi.square.sprite.x < -190) {
        this.pixi.square.sprite.x = -190;
        this.pixi.square.velocity.x = 0;
      }

      if (this.pixi.square.sprite.y > 240) {
        this.pixi.square.sprite.y = 240;
        this.pixi.square.velocity.y = 0;
      }
    });
  },
  beforeDestroy() {
    // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
    // @ts-ignore
    document.body.removeChild(document.querySelector("canvas"));
    this.pixi.squareTexture.destroy();
    this.pixi.square.sprite.destroy();

    delete this.pixi.app;
  },
  data() {
    return {
      pixi: {
        app: new PIXI.Application({ width: 500, height: 500 }),
        container: {},
        square: { sprite: new PIXI.Sprite(), velocity: { x: 0, y: 0 } },
        squareTexture: PIXI.Texture
      },
      moving: {
        up: false,
        down: false,
        left: false,
        right: false
      },
      config: {
        maxVel: 15,
        friction: 0.9,
        acceleration: 2
      },
      type: ""
    };
  }
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
