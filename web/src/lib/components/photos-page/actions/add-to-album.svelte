<script lang="ts">
  import AlbumSelectionModal from '$lib/components/shared-components/album-selection-modal.svelte';
  import MenuOption from '$lib/components/shared-components/context-menu/menu-option.svelte';
  import { addAssetsToAlbum, addAssetsToNewAlbum } from '$lib/utils/asset-utils';
  import type { AlbumResponseDto } from '@immich/sdk';
  import { getMenuContext } from '../asset-select-context-menu.svelte';
  import { getAssetControlContext } from '../asset-select-control-bar.svelte';
  import { mdiImageAlbum, mdiShareVariantOutline } from '@mdi/js';

  export let shared = false;

  let showAlbumPicker = false;

  const { getAssets, clearSelect } = getAssetControlContext();
  const closeMenu = getMenuContext();

  const handleHideAlbumPicker = () => {
    showAlbumPicker = false;
    closeMenu();
  };

  const handleAddToNewAlbum = async (albumName: string) => {
    showAlbumPicker = false;
    closeMenu();

    const assetIds = [...getAssets()].map((asset) => asset.id);
    await addAssetsToNewAlbum(albumName, assetIds);
  };

  const handleAddToAlbum = async (album: AlbumResponseDto) => {
    showAlbumPicker = false;
    const assetIds = [...getAssets()].map((asset) => asset.id);
    await addAssetsToAlbum(album.id, assetIds);
    clearSelect();
  };
</script>

<MenuOption
  on:click={() => (showAlbumPicker = true)}
  text={shared ? 'Add to shared album' : 'Add to album'}
  icon={shared ? mdiShareVariantOutline : mdiImageAlbum}
/>

{#if showAlbumPicker}
  <AlbumSelectionModal
    {shared}
    on:newAlbum={({ detail }) => handleAddToNewAlbum(detail)}
    on:album={({ detail }) => handleAddToAlbum(detail)}
    onClose={handleHideAlbumPicker}
  />
{/if}
