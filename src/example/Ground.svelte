<script lang="ts">
  import { T, useFrame } from '@threlte/core'
  import { useTexture } from '@threlte/extras'

  import Picnic from '$lib/moai_mountain.svelte'

  const map = useTexture('texture.png')
// position={[-650, -30, -900]} for Moai terrain
</script>

<T.Group position={[0, 0, -10]} rotation={[0, 0, 0]}>
  <T.Mesh receiveShadow>
    <T.BoxGeometry args={[100, 100, 1]} />
    <T.MeshStandardMaterial />
  </T.Mesh>    
</T.Group>
