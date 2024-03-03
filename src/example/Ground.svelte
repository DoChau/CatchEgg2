<script lang="ts">
  import { T, useFrame } from '@threlte/core'
  import { useTexture } from '@threlte/extras'

  import Picnic from '$lib/moai_mountain.svelte'

  const map = useTexture('../static/assets/bg rock1.jpg')
// position={[-650, -30, -900]} for Moai terrain
</script>

{#await map then value}
  <T.Group position={[0, 0, -10]} rotation={[0, 0, 0]}>
    <T.Mesh receiveShadow>
      <T.BoxGeometry args={[60, 90, 1]} />
      <T.MeshBasicMaterial map={value} />
    </T.Mesh>    
  </T.Group>
{/await}
