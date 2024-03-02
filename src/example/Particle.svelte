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
  import { writable } from 'svelte/store'
  import type { Euler, Vector3 } from 'three'
  import { BoxGeometry, MeshStandardMaterial, Group } from 'three'
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

  $: rotationCasted = rotation?.toArray() as [x: number, y: number, z: number]
  
</script>

<T.Group bind:this={$dispatchingComponent}
  
  position={position?.toArray()}
  rotation={rotationCasted}
>
  <Bunny/>
</T.Group>
