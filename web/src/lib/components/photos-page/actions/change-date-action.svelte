<script lang="ts">
  import ChangeDate from '$lib/components/shared-components/change-date.svelte';
  import { user } from '$lib/stores/user.store';
  import { getSelectedAssets } from '$lib/utils/asset-utils';
  import { handleError } from '$lib/utils/handle-error';
  import { updateAssets } from '@immich/sdk';
  import { mdiCalendarEditOutline } from '@mdi/js';
  import { DateTime } from 'luxon';
  import { t } from 'svelte-i18n';
  import MenuOption from '../../shared-components/context-menu/menu-option.svelte';
  import { getAssetControlContext } from '../asset-select-control-bar.svelte';
  interface Props {
    menuItem?: boolean;
  }

  let { menuItem = false }: Props = $props();
  const { clearSelect, getOwnedAssets } = getAssetControlContext();

  let isShowChangeDate = $state(false);

  const handleConfirm = async (dateTimeOriginal: string) => {
    isShowChangeDate = false;
    const ids = getSelectedAssets(getOwnedAssets(), $user);

    try {
      await updateAssets({ assetBulkUpdateDto: { ids, dateTimeOriginal } });
    } catch (error) {
      handleError(error, $t('errors.unable_to_change_date'));
    }
    clearSelect();
  };
</script>

{#if menuItem}
  <MenuOption text={$t('change_date')} icon={mdiCalendarEditOutline} onClick={() => (isShowChangeDate = true)} />
{/if}
{#if isShowChangeDate}
  <ChangeDate initialDate={DateTime.now()} onConfirm={handleConfirm} onCancel={() => (isShowChangeDate = false)} />
{/if}
