<script>
  import { onMount } from "svelte";

  const SPEED = 7;

  const patterns = [
    { name: "ON", pattern: "m" },
    { name: "FLUORESCENT FLICKER", pattern: "mmamammmmammamamaaamammma" },
    { name: "FLICKER 1", pattern: "mmnmmommommnonmmonqnmmo" },
    {
      name: "SLOW STRONG PULSE",
      pattern: "abcdefghijklmnopqrstuvwxyzyxwvutsrqponmlkjihgfedcba"
    },
    { name: "CANDLE 1", pattern: "mmmmmaaaaammmmmaaaaaabcdefgabcdefg" },
    { name: "FAST STROBE", pattern: "mamamamamama" },
    { name: "GENTLE PULSE", pattern: "jklmnopqrstuvwxyzyxwvutsrqponmlkj" },
    { name: "FLICKER 2", pattern: "nmonqnmomnmomomno" },
    { name: "CANDLE 2", pattern: "mmmaaaabcdefgmmmmaaaammmaamm" },
    { name: "CANDLE 3", pattern: "mmmaaammmaaammmabcdefaaaammmmabcdefmmmaaaa" },
    { name: "SLOW STROBE", pattern: "aaaaaaaazzzzzzzz" },
    {
      name: "SLOW PULSE NOT FADE TO BLACK",
      pattern: "abcdefghijklmnopqrrqponmlkjihgfedcba"
    }
  ];

  const alpha = "abcdefghijklmnopqrstuvwxyz".split("");

  let choice = 1;
  let counter = 0;
  let pointer = 0;
  let value = 0;
  let op = 0;

  const mapValue = function(value, in_min, in_max, out_min, out_max) {
    return (
      ((value - in_min) * (out_max - out_min)) / (in_max - in_min) + out_min
    );
  };

  const handleClick = () => {
    choice++;
    if (choice >= patterns.length) {
      choice = 0;
    }
  };

  onMount(() => {
    let frame;

    function loop() {
      frame = requestAnimationFrame(loop);

      counter++;
      if (counter % SPEED === 0) {
        value = patterns[choice].pattern[pointer];
        const ai = alpha.indexOf(value);
        op = mapValue(ai, 0, 26, 0, 1);
        pointer++;
        if (pointer >= patterns[choice].pattern.length) {
          pointer = 0;
        }
      }
    }

    loop();

    return () => cancelAnimationFrame(frame);
  });
</script>

<style>
  :global(body) {
    overflow: hidden;
    background: #131313;
    font-family: "VT323", monospace;
    font-size: 24px;
  }

  h1 {
    color: rgb(212, 212, 212);
    text-align: center;
  }

  .frame {
    border: 4px solid #525252;
    border-radius: 5px;
    background: #232323;
    box-shadow: rgba(255, 255, 255, 0.5) 0px 1px 4px;
  }

  .light {
    cursor: pointer;
    background: #f0e68c;
    height: 50px;
    margin: 10px 10px 13px 10px;
    filter: blur(10px);
  }

  .light:last-child {
    margin-bottom: 15px;
  }

  .container {
    text-align: center;
  }

  .btn-pushable {
    position: relative;
    border: none;
    background: transparent;
    padding: 0;
    cursor: pointer;
    outline-offset: 4px;
    transition: filter 250ms;
    user-select: none;
    -webkit-user-select: none;
    touch-action: manipulation;
  }

  .btn-shadow {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border-radius: 12px;
    background: hsl(0deg 0% 0% / 0.25);
    will-change: transform;
    transform: translateY(2px);
    transition: transform 600ms cubic-bezier(0.3, 0.7, 0.4, 1);
  }

  .btn-edge {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border-radius: 12px;
    background: linear-gradient(
      to left,
      hsl(340deg 100% 16%) 0%,
      hsl(340deg 100% 32%) 8%,
      hsl(340deg 100% 32%) 92%,
      hsl(340deg 100% 16%) 100%
    );
  }

  .btn-front {
    display: block;
    position: relative;
    padding: 12px 27px;
    border-radius: 12px;
    font-size: 1.1rem;
    color: white;
    background: hsl(345deg 100% 47%);
    will-change: transform;
    transform: translateY(-4px);
    transition: transform 600ms cubic-bezier(0.3, 0.7, 0.4, 1);
  }

  @media (min-width: 768px) {
    .btn-front {
      font-size: 1.25rem;
      padding: 12px 42px;
    }
  }

  .btn-pushable:hover {
    filter: brightness(110%);
  }

  .btn-pushable:hover .btn-front {
    transform: translateY(-6px);
    transition: transform 250ms cubic-bezier(0.3, 0.7, 0.4, 1.5);
  }

  .btn-pushable:active .btn-front {
    transform: translateY(-2px);
    transition: transform 34ms;
  }

  .btn-pushable:hover .btn-shadow {
    transform: translateY(4px);
    transition: transform 250ms cubic-bezier(0.3, 0.7, 0.4, 1.5);
  }

  .btn-pushable:active .btn-shadow {
    transform: translateY(1px);
    transition: transform 34ms;
  }

  .btn-pushable:focus:not(:focus-visible) {
    outline: none;
  }

  .text {
    font-size: 32px;
  }
</style>

<div class="frame">
  <div class="light" style="opacity:{op}" on:click={handleClick} />
  <div class="light" style="opacity:{op}" on:click={handleClick} />
  <div class="light" style="opacity:{op}" on:click={handleClick} />
</div>

<h1>{patterns[choice].name}</h1>

<div class="container">
  <button class="btn-pushable" role="button" on:click={handleClick}>
    <span class="btn-shadow" />
    <span class="btn-edge" />
    <span class="btn-front text">Next</span>
  </button>
</div>
