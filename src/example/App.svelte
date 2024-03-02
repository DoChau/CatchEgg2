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
	<h1>Pick a bunny</h1>
	<button on:click={() => (state = 'playing')}>Play</button>
{/if}

{#if state === 'paused'}
	<h1>Game paused</h1>
{/if}

{#if state === 'playing'}

  <div>
    <Canvas>
      <World>
		
        <Scene 
          on:won = {(e) => {gameWon()}} 
          on:lose = {(e) => {gameLost()}}
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

  div {
    width: 100vw;
    height: 100vh;
    background: rgb(255, 255, 255);
  }

</style>
