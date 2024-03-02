<script lang="ts">
  import { onMount, createEventDispatcher } from "svelte";
  import { T, useTask } from '@threlte/core'
  import { OrbitControls, AudioListener, interactivity, Environment, Float } from '@threlte/extras'
  import { Debug } from '@threlte/rapier'
  import { Euler, Vector3 } from 'three'

  import Particle from './Particle.svelte'
  import Ground from './Ground.svelte'
  let dispatch = createEventDispatcher()
  let dispatch2 = createEventDispatcher()

  const catched = () => {
        dispatch('won')
    }
  const miss = () => {
        dispatch2('lose')
    }
  const { target } = interactivity()
  

  //Drop the Easter Egg
  const getId = () => {
    return Math.random().toString(16).slice(2)
  }

  const getRandomPosition = () => {
    return new Vector3((Math.random()-0.5)*60, (Math.random()-0.5)*30 , 0)
  }

  const getRandomRotation = () => {
    return new Euler(0, 0, 0)
  }
  type Body = {
      id: string
      mounted: number
      position: Vector3
      rotation: Euler
    }
  let size = 12
  let bodies: Body[] = []
  let lastBodyMounted: number = 0
  let bodyEveryMilliseconds = 1000
  let longevityMilliseconds = 8000

  const body: Body = {
        id: getId(),
        position: getRandomPosition(),
        rotation: getRandomRotation()
      }

  let timer = null
  let countdown = (Math.random()*(10-0 + 1))+0
  let isDelay = true
	onMount(() => {
    console.log ({countdown})
		timer = setInterval(() => {
			countdown -= 1
	  }, 1000)
	})

	$: {
    if (countdown < 1) {
      isDelay = false
			clearInterval(timer)
		}
	}
  //End of drop Easter Egg

  //Create fake Egg
  useTask(() => {
    if (lastBodyMounted + bodyEveryMilliseconds < Date.now()) {
      const body: Body = {
        id: getId(),
        mounted: Date.now(),
        position: getRandomPosition(),
        rotation: getRandomRotation()
      }
      bodies.unshift(body)
      lastBodyMounted = Date.now()
      bodies = bodies
    }
    const deleteIds: string[] = []
    bodies.forEach((body) => {
      if (body.mounted + longevityMilliseconds < Date.now()) {
        deleteIds.push(body.id)
      }
    })

    if (deleteIds.length) {
      deleteIds.forEach((id) => {
        const index = bodies.findIndex((body) => body.id === id)
        if (index !== -1) bodies.splice(index, 1)
      })
      bodies = bodies
    }
  })
</script>

<!-- Equirectangular hdr envmap 
<Environment
  path="/static/assets/"
  files="puresky_4k.hdr"
  isBackground={true}
  format="hdr"
  groundProjection={{ radius: 40, height: 5, scale: { x: 100, y: 100, z: 100 } }}
/>-->

<!-- Equirectangular jpg envmap -->
<Environment
  path="../static/assets/"
  files="bg rock.jpg"
  isBackground={true}
/>


<T.PerspectiveCamera
  makeDefault
  position={[0, 0, 30]}
  fov={90}
    on:create={({ ref }) => {
      ref.lookAt(0, 0, 0)
    }}
>
  <OrbitControls 
    enableZoom={false}
    enableRotate={false} 
  />
</T.PerspectiveCamera>


<T.DirectionalLight
  intensity={2}
  position={[0, 0, 50]}
  castShadow
  shadow.bias={-0.0001}
/>

<Debug/>

<!--Drop fake Egg-->
<Float
  speed={6}
  floatIntensity={1}
  floatingRange={[-2, 2]} 
  >
  {#each bodies as body (body.id)}
    <Particle on:click={miss}
      position={body.position}
      rotation={body.rotation}
    />
  {/each}
</Float>

<!--Drop prize Egg-->
<Float
  speed={5}
  floatIntensity={1}
  floatingRange={[-3, 3]}   
  >
  {#if !isDelay}
    <Particle on:click={catched}
        position={body.position}
        rotation={body.rotation}
      />
  {/if}
</Float>
