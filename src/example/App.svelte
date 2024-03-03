<script lang="ts">
	import { Canvas } from '@threlte/core'
	import { HTML, Environment } from '@threlte/extras'
	import { World } from '@threlte/rapier'
	import Scene from './Scene.svelte'

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


{#if state === 'start'}
	<h1>Egg Catching Game</h1>
	<button on:click={() => (state = 'playing')}>Play</button>
{/if}

{#if state === 'playing'}
	<div class="progress">
		<h1 class="timer" class:pulse={time <= 5}>
			{time}
		</h1>
	</div>
  <div class="game">
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
        <Scene 
			on:won = {(e) => {gameWon()}} 
			on:lost = {(e) => {gameLost()}}
		/>

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
	.game {
		width: 100vw;
		height: 100vh;
		background: rgb(13, 19, 32);
		background: linear-gradient(180deg, rgba(13, 19, 32, 1) 0%, rgba(8, 12, 21, 1) 100%);
	}

	button{
		display: block;
		justify-self: center;
	}
	.progress{
		position: absolute; 
		left: 45%; 
		bottom: 150px; 
		justify-content: center;		
	}
	.timer {
		
		transition: color 0.3s ease;
		width:80px;
		height:80px;
	}

	.pulse {
		
		color: var(--pulse);
		width:80px;
		height:80px;
		animation: pulse 1s infinite ease;
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
