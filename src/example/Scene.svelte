<script lang="ts">
  import { onMount, createEventDispatcher } from "svelte";
  import { T } from '@threlte/core'
  import { OrbitControls, AudioListener, interactivity, Environment } from '@threlte/extras'
  import { Debug } from '@threlte/rapier'
  import { Euler, Vector3 } from 'three'

  import Particle from './Particle.svelte'
  import Emitter from './Emitter.svelte'
  import Ground from './Ground.svelte'
  let dispatch = createEventDispatcher()
  const catched = () => {
        dispatch('won')
    }
  const { target } = interactivity()
  
  import Stars from './Stars.svelte'

  
  //Float
  import { Float, Grid, useGltf } from '@threlte/extras'
  import type { Mesh } from 'three'
  import Blob from './Blob.svelte'
  type Nodes = 'ball-1' | 'ball-2' | 'ball-3' | 'ball-4' | 'ball-5'

  const gltf = useGltf<{
    nodes: Record<Nodes, Mesh>
    materials: {}
  }>('/static/assets/cute_bunny.glb', {
    useDraco: true
  })


  //Drop the Easter Egg
  const getId = () => {
    return Math.random().toString(16).slice(2)
  }

  const getRandomPosition = () => {
    return new Vector3(0.5 - Math.random() * 1, 5 - Math.random() * 1 + 10, 0.5 - Math.random() * 1)
  }

  const getRandomRotation = () => {
    return new Euler(Math.random() * 10, Math.random() * 10, Math.random() * 10)
  }
  type Body = {
      id: string
      mounted: number
      position: Vector3
      rotation: Euler
    }
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
</script>
<Environment
  path="/hdr/"
  files="shanghai_riverside_1k.hdr"
/>

<Float
  rotationIntensity={0.15}
  rotationSpeed={2}
>
<T.PerspectiveCamera
  makeDefault
  position={[10, 10, 10]}
>
  <OrbitControls 
    enableZoom={false}
    enableRotate={true} 
  />
  <AudioListener />
</T.PerspectiveCamera>
</Float>

<T.DirectionalLight
  intensity={2}
  position={[10, 10, 10]}
  castShadow
  shadow.bias={-0.0001}
/>
<T.AmbientLight intensity={0.3} />

<Grid
  position.y={-10}
  sectionThickness={1}
  infiniteGrid
  cellColor="#dddddd"
  sectionColor="#ffffff"
  sectionSize={10}
  cellSize={2}
/>


{#if $gltf}
  {#each Object.values($gltf.nodes) as node}
    {#if node.geometry}
      <Blob geometry={node.geometry} />
    {/if}
  {/each}
{/if}


{#if !isDelay}
  <Particle on:click={catched}
      position={body.position}
      rotation={body.rotation}
    />
{/if}