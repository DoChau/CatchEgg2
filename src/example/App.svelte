<script lang="ts">
  import { Canvas } from '@threlte/core'
  import { HTML, Environment } from '@threlte/extras'
  import { World } from '@threlte/rapier'
  import { muted } from './Particle.svelte'
  import Scene from './Scene.svelte'
  import { Pane, Button } from 'svelte-tweakpane-ui'
  import { emoji } from './emoji.ts'

	type State = 'start' | 'playing' | 'paused' | 'won' | 'lost'

	let state: State = 'start'
	
	let timerId: number | null = null
	let time = 15

	let list=['Ashley','Andy','Andrew','Chad','Hiep','Sarah','David']
  let info
  export function Info() {
      info = list[Math.floor(Math.random() * list.length)]
      console.log({info})
      return info
    }
		
	function startGameTimer() {
		function countdown() {
			state !== 'paused' && (time -= 1)
		}
		timerId = setInterval(countdown, 1000)
	}

	function pauseGame(e: KeyboardEvent) {
		if (e.key === 'Escape') {
			switch (state) {
				case 'playing':
					state = 'paused'
					break
				case 'paused':
					state = 'playing'
					break
			}
		}
	}

	function resetGame() {
		timerId && clearInterval(timerId)
		timerId = null
		time = 15
	}

	function gameWon() {
		state = 'won'
		resetGame()
	}

	function gameLost() {
		state = 'lost'
    Info()
		resetGame()
	}

	$: if (state === 'playing') {
		//	in case you pause the game
		!timerId && startGameTimer()
	}

	$: time === 0 && gameLost()

</script>


<svelte:window on:keydown={pauseGame} />

{#if state === 'start'}
	<h1>Egg Catching Game</h1>
	<button on:click={() => (state = 'playing')}>Play</button>
{/if}

{#if state === 'paused'}
	<h1>Game paused</h1>
{/if}

{#if state === 'playing'}

  <Pane
    title="Rigid Body"
    position="fixed"
  >
    <Button
      title="toggle sound"
      on:click={() => {
        $muted = !$muted
      }}
    />
  </Pane>

  <div>
    <Canvas>
      <World>
							
				<!-- Equirectangular jpg envmap 
				<Environment
					path="./static/assets/"
					files="bg.jpg"
					isBackground={true}
					groundProjection={{ radius: 200, height: 50, scale: { x: 100, y: 100, z: 100 } }}
				/>
				-->
				
        <Scene on:won = {(e) => {gameWon()}} />

        <HTML
          slot="fallback"
          transform
        >
          <p>
            It seems your browser<br />
            doesn't support WASM.<br />
            I'm sorry.
          </p>
        </HTML>
      </World>
    </Canvas>
  </div>
{/if}

{#if state === 'lost'}
	<h1>Good luck next time! 💩</h1>
  <h1>{info}</h1>
	<button on:click={() => (state = 'playing')}>Play again</button>
{/if}

{#if state === 'won'}
	<h1>You've got the Easter Egg'! 🎉</h1>
	<button on:click={() => (state = 'playing')}>Play again</button>
{/if}

<style>
  :global(body) {
    margin: 0;
  }

  div {
    width: 100vw;
    height: 100vh;
    background: rgb(13, 19, 32);
    background: linear-gradient(180deg, rgba(13, 19, 32, 1) 0%, rgba(8, 12, 21, 1) 100%);
  }

	.progress{
		display: flex;
		justify-content: center;		
	}
	.timer {
		transition: color 0.3s ease;
		width:80px;
		height:80px;
	}

	.timerround {
		background: -webkit-linear-gradient(left, skyBlue 50%, #eee 50%);
		border-radius: 100%;
		height: calc(var(--size) * 1px);
		width: calc(var(--size) * 1px);
		margin-top: -30px;
		margin-left: -115px;
		-webkit-animation: timeround calc(var(--duration) * 1s) steps(1000, start) infinite;
		-webkit-mask: radial-gradient(transparent 50%,#000 50%);
		mask: radial-gradient(transparent 50%,#000 50%);
	}
	.mask {
		border-radius: 100% 0 0 100% / 50% 0 0 50%;
		height: 100%;
		left: 0;
		position: absolute;
		top: 0;
		width: 50%;
		-webkit-animation: mask calc(var(--duration) * 1s) steps(500, start) infinite;
		-webkit-transform-origin: 100% 50%;
	}
	
	.pulse {
		color: var(--pulse);
		width:80px;
		height:80px;
		animation: pulse 1s infinite ease;
	}
	.pulseround {
		display:none;
	}

	@keyframes pulse {
		to {
			scale: 1.4;
		}
	}

	@keyframes morph {
		0% {
			border-radius:  60% 40% 30% 70% / 60% 30% 70% 40%;
			background: linear-gradient(45deg, var(--primary) 0%, var(--secondary) 100%);
		} 
		
		50% {
			border-radius:  30% 60% 70% 40% / 50% 60% 30% 60%;
			background: linear-gradient(45deg, var(--third) 0%, var(--secondary) 100%);
		}
		
		100% {
			border-radius:  60% 40% 30% 70% / 60% 30% 70% 40%;
			background: linear-gradient(45deg, var(--primary) 0%, var(--secondary) 100%);
		} 
	}

	@-webkit-keyframes timeround {
		100% {
			-webkit-transform: rotate(360deg);
		}
	}
	@-webkit-keyframes mask {
		0% {
			background: #eee;
			-webkit-transform: rotate(0deg);
		}
		50% {
			background: #eee;
			-webkit-transform: rotate(-180deg);
		}
		50.01% {
			background: skyBlue;
			-webkit-transform: rotate(0deg);
		}
		100% {
			background: skyBlue;
			-webkit-transform: rotate(-180deg);
		}
	}
	

</style>
