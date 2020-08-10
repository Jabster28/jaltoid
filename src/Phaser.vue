<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png" />
    teeee -->
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import * as Phaser from "phaser";

interface This {
  [key: string]: unknown; // (A)
  game: Phaser.Game;
  square: Phaser.Physics.Arcade.Sprite | undefined;
  moving: {
    up: boolean;
    down: boolean;
    left: boolean;
    right: boolean;
  };
  vel: {
    x: number;
    y: number;
  };
  maxVel: number;
  acc: number;
  friction: number;
}

export default Vue.extend({
  mounted() {
    window.app = this;
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
    // app.ticker.add(delta => {
    //   if (this.moving.up && this.pixi.square.sprite.y == 240)
    //     this.vel.y += 13a;
    //   // if (this.moving.down)
    //   //   this.vel.y -= this.config.acceleration;
    //   if (this.moving.right)
    //     this.vel.x += this.config.acceleration;
    //   if (this.moving.left)
    //     this.vel.x -= this.config.acceleration;
    //   // rotate the container!
    //   // use delta to create frame-independent transform
    //   // container.rotation -= 0.05 * delta;

    //   // slidiness
    //   if (this.vel.y > 1) {
    //     this.vel.y -= 0.6;
    //   } else {
    //     //   this.vel.y += 0.1;
    //     // } else if (this.vel.y != 0) {
    //     this.vel.y = -11;
    //   }
    //   if (this.vel.x > 1) {
    //     this.vel.x -= 0.1;
    //   } else if (this.vel.x < 0) {
    //     this.vel.x += 0.1;
    //   } else if (this.vel.x != 0) {
    //     this.vel.x = 0;
    //   }

    //   // cap velocity at this.maxVel
    //   if (this.vel.y > this.maxVel)
    //     this.vel.y = this.maxVel;
    //   if (this.vel.x > this.maxVel)
    //     this.vel.x = this.maxVel;
    //   if (this.vel.y < -this.maxVel)
    //     this.vel.y = -this.maxVel;
    //   if (this.vel.x < -this.maxVel)
    //     this.vel.x = -this.maxVel;

    //   this.pixi.square.sprite.y -= this.vel.y;
    //   this.pixi.square.sprite.x += this.vel.x;

    //   if (this.pixi.square.sprite.x > 240) {
    //     this.pixi.square.sprite.x = 240;
    //     this.vel.x = 0;
    //   }

    //   if (this.pixi.square.sprite.y < -190) {
    //     this.pixi.square.sprite.y = -190;
    //     this.vel.y = 0;
    //   }

    //   if (this.pixi.square.sprite.x < -190) {
    //     this.pixi.square.sprite.x = -190;
    //     this.vel.x = 0;
    //   }

    //   if (this.pixi.square.sprite.y > 240) {
    //     this.pixi.square.sprite.y = 240;
    //     this.vel.y = 0;
    //   }
    // });
  },
  data() {
    // eslint-disable-next-line @typescript-eslint/no-this-alias
    const self = this;
    const x: This = {
      game: new Phaser.Game({
        width: 960,
        height: 640,
        scale: {
          mode: Phaser.Scale.FIT,
          autoCenter: Phaser.Scale.CENTER_BOTH
        },
        physics: {
          default: "arcade",
          arcade: {
            debug: true,
            gravity: { y: 1200 }
          }
        },
        type: Phaser.AUTO,
        scene: {
          preload: self.preload,
          create: self.create,
          update: self.update
        }
      }),
      moving: {
        up: false,
        down: false,
        left: false,
        right: false
      },
      vel: {
        x: 0,
        y: 0
      },
      type: "",
      square: undefined,
      maxVel: 100,
      acc: 70,
      friction: 50
    };
    return x;
  },
  beforeDestroy() {
    this.game.destroy(true);
  },
  methods: {
    preload() {
      this.game.scene.scenes[0].load.image("square", "square.png");
      // this.game.
    },
    create() {
      const group = this.game.scene.scenes[0].physics.add.group({
        defaultKey: "square",
        bounceX: 0.15,
        bounceY: 0.01,
        collideWorldBounds: true
      });
      this.square = group.create(350, 300);
    },
    update() {
      this.vel.x = this.square?.body.velocity.x || 0;
      if (this.moving.right) {
        this.vel.x += this.acc;
        this.square?.setVelocityX(this.vel.x);
      } else if (this.moving.left) {
        this.vel.x -= this.acc;
        this.square?.setVelocityX(this.vel.x);
      } else {
        if (this.vel.x > this.friction) {
          this.vel.x -= this.friction;
        } else if (this.vel.x < -this.friction) {
          this.vel.x += this.friction;
        } else {
          this.vel.x = 0;
        }
        this.square?.setVelocityX(this.vel.x);
      }
      if (
        this.moving.up &&
        (this.square?.body.onFloor() || this.square?.body.touching.down)
      ) {
        this.square.setVelocityY(-700);
      }
      // if (this.vel.x > 1) {
      //   this.vel.x -= this.friction;
      // } else if (this.vel.x < 0) {
      //   this.vel.x += this.friction;
      // } else {
      //   this.vel.x = 0;
      //   this.square?.setVelocityX(0);
      // }
    }
  }
});
</script>

<style>
* {
  margin: 0;
  padding: 0;
  overflow: hidden;
  background-color: black;
}
</style>
