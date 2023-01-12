<script lang="ts">
  import { onMount } from 'svelte';

  import songMp3 from '../assets/song.mp3';
  import bum from '../assets/bum.mp3';

  let isStart = false;

  const initBg = () => {
    const canvas: any = document.querySelector('#cvs');
    let context = canvas.getContext('2d');

    context.fillStyle = '#112';
    context.fillRect(0, canvas.height, 10, -35);
    context.fillRect(10, canvas.height, 10, -15);
    context.fillRect(20, canvas.height, 10, -20);
    context.fillRect(30, canvas.height, 10, -30);
    context.fillRect(40, canvas.height, 10, -25);
    context.fillRect(50, canvas.height, 10, -10);
    context.fillRect(60, canvas.height, 10, -20);
    context.fillRect(70, canvas.height, 10, -35);
    context.fillRect(80, canvas.height, 10, -35);
    context.fillRect(90, canvas.height, 10, -15);
    context.fillRect(100, canvas.height, 10, -20);
    context.fillRect(110, canvas.height, 10, -35);
    context.fillRect(120, canvas.height, 10, -10);
    context.fillRect(130, canvas.height, 10, -15);
    context.fillRect(140, canvas.height, 10, -10);
    context.fillRect(150, canvas.height, 10, -35);
    context.fillRect(160, canvas.height, 10, -20);
    context.fillRect(170, canvas.height, 10, -15);
    context.fillRect(180, canvas.height, 10, -25);
    context.fillRect(190, canvas.height, 10, -20);
    context.fillRect(200, canvas.height, 10, -25);
    context.fillRect(210, canvas.height, 10, -10);
    context.fillRect(220, canvas.height, 10, -20);
    context.fillRect(230, canvas.height, 10, -35);
    context.fillRect(240, canvas.height, 10, -25);
    context.fillRect(250, canvas.height, 10, -20);
    context.fillRect(260, canvas.height, 10, -25);
    context.fillRect(270, canvas.height, 10, -10);
    context.fillRect(280, canvas.height, 10, -20);
    context.fillRect(290, canvas.height, 10, -35);
  };

  const initFireWork = () => {
    const c: any = document.querySelector('#Canvas');
    const ctx = c.getContext('2d');

    var cwidth: number, cheight: number;
    var shells: any[] = [];
    var pass: any[] = [];

    var colors = [
      '#FF5252',
      '#FF4081',
      '#E040FB',
      '#7C4DFF',
      '#536DFE',
      '#448AFF',
      '#40C4FF',
      '#18FFFF',
      '#64FFDA',
      '#69F0AE',
      '#B2FF59',
      '#EEFF41',
      '#FFFF00',
      '#FFD740',
      '#FFAB40',
      '#FF6E40',
    ];

    window.onresize = function () {
      reset();
    };
    reset();
    function reset() {
      cwidth = window.innerWidth;
      cheight = window.innerHeight;
      c.width = cwidth;
      c.height = cheight;
    }

    function newShell() {
      var left: any = Math.random() > 0.5;
      var shell: any = {};
      shell.x = 1 * left;
      shell.y = 1;
      shell.xoff = (0.01 + Math.random() * 0.007) * (left ? 1 : -1);
      shell.yoff = 0.012 + Math.random() * 0.007;
      shell.size = Math.random() * 6 + 1;
      shell.color = colors[Math.floor(Math.random() * colors.length)];

      shells.push(shell);
    }

    function newPass(shell: {
      size: number;
      x: number;
      y: number;
      color: any;
    }) {
      var pasCount = Math.ceil(Math.pow(shell.size, 2) * Math.PI);

      for (let i = 0; i < pasCount; i++) {
        var pas: any = {};
        pas.x = shell.x * cwidth;
        pas.y = shell.y * cheight;

        var a = Math.random() * 4;
        var s = Math.random() * 10;

        pas.xoff = s * Math.sin((5 - a) * (Math.PI / 2));
        pas.yoff = s * Math.sin(a * (Math.PI / 2));

        pas.color = shell.color;
        pas.size = Math.sqrt(shell.size);

        if (pass.length < 1000) {
          pass.push(pas);
        }
      }
    }

    var lastRun = 0;

    function Run() {
      var audio = new Audio(bum);
      var dt = 1;
      if (lastRun != 0) {
        dt = Math.min(50, performance.now() - lastRun);
      }
      lastRun = performance.now();

      //ctx.clearRect(0, 0, cwidth, cheight);
      ctx.fillStyle = 'rgba(0,0,0,0.15)';

      ctx.fillRect(0, 0, cwidth, cheight);

      if (shells.length < 10 && Math.random() > 0.96) {
        newShell();
      }

      for (let ix in shells) {
        var shell = shells[ix];

        ctx.beginPath();
        ctx.arc(
          shell.x * cwidth,
          shell.y * cheight,
          shell.size,
          0,
          2 * Math.PI
        );
        ctx.fillStyle = shell.color;
        ctx.fill();

        shell.x -= shell.xoff;
        shell.y -= shell.yoff;
        shell.xoff -= shell.xoff * dt * 0.001;
        shell.yoff -= (shell.yoff + 0.2) * dt * 0.00005;

        if (shell.yoff < -0.005) {
          newPass(shell);
          audio.volume = 0.2;
          audio.play();
          shells.splice(+ix, 1);
        }
      }

      for (let ix in pass) {
        var pas = pass[ix];
        ctx.beginPath();
        ctx.arc(pas.x, pas.y, pas.size, 0, 2 * Math.PI);
        ctx.fillStyle = pas.color;
        ctx.fill();

        pas.x -= pas.xoff;
        pas.y -= pas.yoff;
        pas.xoff -= pas.xoff * dt * 0.001;
        pas.yoff -= (pas.yoff + 5) * dt * 0.0005;
        pas.size -= dt * 0.002 * Math.random();

        if (pas.y > cheight || pas.y < -50 || pas.size <= 0) {
          pass.splice(+ix, 1);
        }
      }
      requestAnimationFrame(Run);
    }

    Run();
  };

  onMount(() => {
    initBg();
  });

  const onStart = () => {
    var songMp3Audio = new Audio(songMp3);
    songMp3Audio.play();

    initFireWork();

    isStart = true;
  };
</script>

<canvas id="Canvas" />

<canvas id="cvs" />

<button class="btn" hidden={isStart} on:click={onStart}> Click </button>

<style>
  canvas {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
  }

  #cvs {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    background-color: none;
  }

  .btn {
    position: absolute;
    z-index: 999;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    border: 2px solid purple;
    border-radius: 5px;
    font-size: 18px;
    padding: 10px;
    max-width: 250px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    transition: all 0.3s ease;
    color: purple;
  }
</style>
