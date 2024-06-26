<script setup lang="ts">
import { TreasuryWallet } from '@/helpers/interfaces';
import { lookupAddress } from '@/helpers/utils';

const props = defineProps<{
  wallets: TreasuryWallet[];
  admins: string[];
}>();

const { web3Account } = useWeb3();

const ensAddresses = ref<{ [k: string]: string } | null>(null);

onMounted(async () => {
  ensAddresses.value = await lookupAddress(props.wallets.map(w => w.address));
});
</script>

<template>
  <h1 class="hidden lg:mb-3 lg:block">
    {{ $t('treasury.title') }}
  </h1>

  <BaseBlock
    v-if="wallets.length"
    :title="$t('treasury.wallets.title')"
    :counter="wallets.length"
    :label="$t('treasury.24hChange')"
    data-testid="treasury-wallets-block"
    slim
  >
    <ul>
      <TreasuryWalletsListItem
        v-for="wallet in wallets"
        :key="wallet.address"
        :wallet="wallet"
        :ens-address="ensAddresses?.[wallet.address]"
      />
    </ul>
  </BaseBlock>
  <BaseBlock
    v-else
    class="text-center"
    data-testid="treasury-wallets-message-block"
  >
    <div>
      <div>
        {{ $t('treasury.wallets.empty') }}
      </div>
      <BaseButton
        v-if="admins?.includes(web3Account)"
        class="mt-3"
        @click="$router.push({ name: 'spaceSettings' })"
      >
        {{ $t('treasury.wallets.addTreasury') }}
      </BaseButton>
    </div>
  </BaseBlock>
</template>
