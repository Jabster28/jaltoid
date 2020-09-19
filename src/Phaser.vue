<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png" />
    teeee -->
  </div>
</template>

<script lang="ts">
/* eslint-disable @typescript-eslint/ban-ts-ignore */
import Vue from 'vue';
import * as Phaser from 'phaser';

interface This {
  [key: string]: unknown; // (A)
  game: Phaser.Game;
  emi: Phaser.Physics.Arcade.Sprite | undefined;
  dalton: Phaser.Physics.Arcade.Sprite | undefined;
  emimove: {
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
  };
  daltonmove: {
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
  };
  acc: number;
  friction: number;
  platforms: Phaser.Physics.Arcade.StaticGroup | undefined;
}

export default Vue.extend({
  mounted() {
    window.app = this;
    // listen for keys
    window.addEventListener('keydown', e => {
      e = e || window.event;
      console.log(e.keyCode);

      switch (e.keyCode) {
        case 87:
          this.daltonmove.moving.up = true;
          break;
        case 65:
          this.daltonmove.moving.left = true;
          break;
        case 83:
          this.daltonmove.moving.down = true;
          break;
        case 68:
          this.daltonmove.moving.right = true;
          break;
        case 38:
          this.emimove.moving.up = true;
          break;
        case 40:
          this.emimove.moving.down = true;
          break;
        case 37:
          this.emimove.moving.left = true;
          break;
        case 39:
          this.emimove.moving.right = true;
          break;
        case 192:
          if (!document.fullscreenElement)
            document.querySelector('canvas')?.requestFullscreen();
          else document.exitFullscreen();
      }
    });
    window.addEventListener('keyup', e => {
      e = e || window.event;
      switch (e.keyCode) {
        case 87:
          this.daltonmove.moving.up = false;
          break;
        case 65:
          this.daltonmove.moving.left = false;
          this.dalton?.anims.play('daltonidleLeft', true);
          break;
        case 83:
          this.daltonmove.moving.down = false;
          break;
        case 68:
          this.daltonmove.moving.right = false;
          this.dalton?.anims.play('daltonidleRight', true);
          break;
        case 38:
          this.emimove.moving.up = false;
          break;
        case 40:
          this.emimove.moving.down = false;
          break;
        case 37:
          this.emimove.moving.left = false;
          this.emi?.anims.play('idleLeft', true);
          break;
        case 39:
          this.emimove.moving.right = false;
          this.emi?.anims.play('idleRight', true);
          break;
      }
    });
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
          autoCenter: Phaser.Scale.CENTER_BOTH,
        },
        physics: {
          default: 'arcade',
          arcade: {
            debug: true,
            gravity: {y: 1200},
          },
        },
        type: Phaser.AUTO,
        scene: {
          // @ts-ignore
          preload: self.preload,
          // @ts-ignore
          create: self.create,
          // @ts-ignore
          update: self.update,
        },
      }),

      emimove: {
        vel: {
          x: 0,
          y: 0,
        },
        moving: {
          up: false,
          down: false,
          left: false,
          right: false,
        },
      },

      daltonmove: {
        vel: {
          x: 0,
          y: 0,
        },
        moving: {
          up: false,
          down: false,
          left: false,
          right: false,
        },
      },

      type: '',
      emi: undefined,
      acc: 0.5,
      friction: 50,
      platforms: undefined,
    };
    return x;
  },
  beforeDestroy() {
    this.game.destroy(true);
  },
  methods: {
    sleep(ms: number) {
      const start = new Date().getTime(),
        expire = start + ms;
      while (new Date().getTime() < expire) {
        /* */
      }
      return;
    },
    preload() {
      const scene: Phaser.Scene = this.game.scene.scenes[0];
      scene.load.image('square', 'square.png');
      scene.load.spritesheet('emi', 'emi.png', {
        frameHeight: 100,
        frameWidth: 100,
      });
      scene.load.spritesheet('dalton', 'dalton.png', {
        frameHeight: 130,
        frameWidth: 100,
      });
    },
    create() {
      // make world
      const scene: Phaser.Scene = this.game.scene.scenes[0];
      this.game.scene.scenes[0].physics.world.setBounds(0, 0, 2000, 640);

      // sample text
      scene.add.text(100, 320, '100', {color: '#000'});
      scene.add.text(200, 320, '200', {color: '#000'});
      scene.add.text(300, 320, '300', {color: '#000'});
      scene.add.text(400, 320, '400', {color: '#000'});
      scene.add.text(500, 320, '500', {color: '#000'});
      scene.add.text(600, 320, '600', {color: '#000'});
      scene.add.text(700, 320, '700', {color: '#000'});
      scene.add.text(800, 320, '800', {color: '#000'});
      scene.add.text(900, 320, '900', {color: '#000'});
      scene.add.text(1000, 320, '1000', {color: '#000'});
      scene.add.text(1100, 320, '00', {color: '#000'});
      scene.add.text(1200, 320, '00', {color: '#000'});
      scene.add.text(1300, 320, '00', {color: '#000'});
      scene.add.text(1400, 320, '00', {color: '#000'});
      scene.add.text(1500, 320, '00', {color: '#000'});
      scene.add.text(1600, 320, '00', {color: '#000'});
      scene.add.text(1700, 320, '00', {color: '#000'});

      // initialising platforms
      this.platforms = scene.physics.add.staticGroup();
      this.platforms
        .create(400, 2000, 'square')
        .setScale(55)
        .refreshBody();
      this.platforms.create(600, 480, 'square');
      this.platforms.create(50, 250, 'square');
      this.platforms.create(750, 280, 'square');

      // setting up emi
      const group = this.game.scene.scenes[0].physics.add.group({
        defaultKey: 'emi',
        bounceX: 0.15,
        bounceY: 0.01,
        collideWorldBounds: true,
      });
      this.emi = group.create(350, 300);
      scene.physics.add.collider(this.emi, this.platforms);
      this.game.scene.scenes[0].anims.create({
        key: 'idleLeft',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('emi', {
          start: 0,
          end: 1,
        }),
        frameRate: 2,
        repeat: -1,
      });
      this.game.scene.scenes[0].anims.create({
        key: 'idleRight',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('emi', {
          start: 2,
          end: 3,
        }),
        frameRate: 2,
        repeat: -1,
      });
      this.game.scene.scenes[0].anims.create({
        key: 'left',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('emi', {
          start: 4,
          end: 5,
        }),
        frameRate: 4,
        repeat: -1,
      });
      this.game.scene.scenes[0].anims.create({
        key: 'right',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('emi', {
          start: 6,
          end: 7,
        }),
        frameRate: 4,
        repeat: -1,
      });
      this.emi?.anims.play('idleRight', true);

      // datlon
      const dgroup = this.game.scene.scenes[0].physics.add.group({
        defaultKey: 'dalton',
        bounceX: 0.15,
        bounceY: 0.01,
        collideWorldBounds: true,
      });
      this.dalton = dgroup.create(600, 300);
      scene.physics.add.collider(this.dalton, this.platforms);
      this.game.scene.scenes[0].anims.create({
        key: 'daltonidleLeft',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('dalton', {
          start: 0,
          end: 1,
        }),
        frameRate: 2,
        repeat: -1,
      });
      this.game.scene.scenes[0].anims.create({
        key: 'daltonidleRight',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('dalton', {
          start: 2,
          end: 3,
        }),
        frameRate: 2,
        repeat: -1,
      });
      this.game.scene.scenes[0].anims.create({
        key: 'daltonleft',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('dalton', {
          start: 4,
          end: 5,
        }),
        frameRate: 4,
        repeat: -1,
      });
      this.game.scene.scenes[0].anims.create({
        key: 'daltonright',
        frames: this.game.scene.scenes[0].anims.generateFrameNumbers('dalton', {
          start: 6,
          end: 7,
        }),
        frameRate: 4,
        repeat: -1,
      });
      this.dalton?.anims.play('daltonidleRight', true);

      // camera
      this.game.scene.scenes[0].cameras.main.startFollow(this.dalton);
      scene.cameras.main.setBounds(0, 0, 2000, 640);
      scene.cameras.main.setLerp(0.2, 0.2);
      scene.cameras.main.setFollowOffset(0, 320);
      scene.cameras.main.setBackgroundColor('#fff');
    },
    update(t: number, d: number) {
      this.emimove.vel.x = this.emi?.body.velocity.x || 0;
      if (this.emimove.moving.right) {
        this.emimove.vel.x += this.acc * d;
        this.emi?.anims.play('right', true);
        this.emi?.setVelocityX(this.emimove.vel.x);
      } else if (this.emimove.moving.left) {
        this.emimove.vel.x -= this.acc * d;
        this.emi?.anims.play('left', true);
        this.emi?.setVelocityX(this.emimove.vel.x);
      } else {
        if (this.emimove.vel.x > this.friction) {
          this.emimove.vel.x -= this.friction;
        } else if (this.emimove.vel.x < -this.friction) {
          this.emimove.vel.x += this.friction;
        } else {
          this.emimove.vel.x = 0;
        }
        this.emi?.setVelocityX(this.emimove.vel.x);
      }
      if (
        this.emimove.moving.up &&
        (this.emi?.body.onFloor() || this.emi?.body.touching.down)
      ) {
        this.emi.setVelocityY(-1000);
      }

      this.daltonmove.vel.x = this.dalton?.body.velocity.x || 0;
      if (this.daltonmove.moving.right) {
        this.daltonmove.vel.x += (this.acc + 0.7) * d;
        this.dalton?.anims.play('daltonright', true);
        this.dalton?.setVelocityX(this.daltonmove.vel.x);
      } else if (this.daltonmove.moving.left) {
        this.daltonmove.vel.x -= (this.acc + 0.7) * d;
        this.dalton?.anims.play('daltonleft', true);
        this.dalton?.setVelocityX(this.daltonmove.vel.x);
      } else {
        if (this.daltonmove.vel.x > this.friction) {
          this.daltonmove.vel.x -= this.friction;
        } else if (this.daltonmove.vel.x < -this.friction) {
          this.daltonmove.vel.x += this.friction;
        } else {
          this.daltonmove.vel.x = 0;
        }
        this.dalton?.setVelocityX(this.daltonmove.vel.x);
      }
      if (
        this.daltonmove.moving.up &&
        (this.dalton?.body.onFloor() || this.dalton?.body.touching.down)
      ) {
        this.dalton.setVelocityY(-700);
      }
      // if (this.vel.x > 1) {
      //   this.vel.x -= this.friction;
      // } else if (this.vel.x < 0) {
      //   this.vel.x += this.friction;
      // } else {
      //   this.vel.x = 0;
      //   this.emi?.setVelocityX(0);
      // }
    },
  },
});
</script>

<style>
* {
  margin: 0;
  padding: 0;
  overflow: hidden;
}
</style>
