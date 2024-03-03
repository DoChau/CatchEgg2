<script
  lang="ts"
  context="module"
>
</script>

<script lang="ts">
  import { T, forwardEventHandlers, useFrame } from '@threlte/core'
  import type { Euler, Vector3 } from 'three'
  import Bunny from '$lib/cute_bunny.svelte'
  const dispatchingComponent = forwardEventHandlers()

  export let position: Vector3 | undefined = undefined
  export let rotation: Euler | undefined = undefined


  $: rotationCasted = rotation?.toArray() as [x: number, y: number, z: number]
  
</script>

<T.Group bind:this={$dispatchingComponent}
  
  position={position?.toArray()}
  rotation={rotationCasted}
>
  <Bunny/>
</T.Group>
