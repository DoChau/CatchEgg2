<script
  lang="ts"
  context="module"
>
  const geometry = new BoxGeometry(1, 1, 1)
  const material = new MeshStandardMaterial()
  export const muted = writable(true)
  
</script>

<script lang="ts">
  import { T, forwardEventHandlers, useFrame } from '@threlte/core'
  import { PositionalAudio, useGltf } from '@threlte/extras'
  import { Collider, RigidBody, type ContactEvent } from '@threlte/rapier'
  import { writable } from 'svelte/store'
  import type { Euler, Vector3 } from 'three'
  import { BoxGeometry, MeshStandardMaterial, Group } from 'three'
  import { clamp } from 'three/src/math/MathUtils'
  import Egg from '$lib/egg.svelte'
  import Bunny from '$lib/cute_bunny.svelte'
  const dispatchingComponent = forwardEventHandlers()

  export let position: Vector3 | undefined = undefined
  export let rotation: Euler | undefined = undefined
  
  //for rendering Eggs
  export const ref = new Group()

  const audios: {
    threshold: number
    volume: number
    stop: (() => any) | undefined
    play: ((...args: any[]) => any) | undefined
    source: string
  }[] = new Array(9).fill(0).map((_, i) => {
    return {
      threshold: i / 10,
      play: undefined,
      stop: undefined,
      volume: (i + 2) / 10,
      source: `../static/assets/ping.mp3`
    }
  })

  const fireSound = (e: ContactEvent) => {
    if ($muted) return
    const volume = clamp((e.detail.totalForceMagnitude - 30) / 1100, 0.1, 1)
    const audio = audios.find((a) => a.volume >= volume)
    audio?.stop?.()
    audio?.play?.()
  }

  $: rotationCasted = rotation?.toArray() as [x: number, y: number, z: number]
  
</script>

<T.Group bind:this={$dispatchingComponent}
  
  position={position?.toArray()}
  rotation={rotationCasted}
>
  <RigidBody
    type={'dynamic'}
    on:contact={fireSound}
  >
    {#each audios as audio}
      <PositionalAudio
        autoplay={false}
        detune={600 - Math.random() * 1200}
        bind:stop={audio.stop}
        bind:play={audio.play}
        src={audio.source}
        volume={audio.volume}
      />
    {/each}

    <Collider
      contactForceEventThreshold={30}
      restitution={0.4}
      shape={'ball'}
      args={[1, 1, 1]}
    />
    <!--
    <T.Mesh 
      castShadow
      receiveShadow
      {geometry}
      {material}
    />
    -->
    <Bunny/>
  </RigidBody>
</T.Group>
