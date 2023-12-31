<script lang="ts" setup>
import { formatUnits } from '@ethersproject/units';
import { computed, ref } from 'vue';
import { useI18n } from 'vue-i18n';

import { ColumnDefinition } from '@/components/_global/BalTable/types';

import ClaimRewardsBtn from '@/components/btns/ClaimRewardsBtn/ClaimRewardsBtn.vue';
import useBreakpoints from '@/composables/useBreakpoints';
import useNumbers, { FNumFormats } from '@/composables/useNumbers';
import { useTokens } from '@/providers/tokens.provider';
import { bnum } from '@/lib/utils';
import { Gauge } from '@/services/balancer/gauges/types';
import { TokenInfo } from '@/types/TokenList';

/**
 * TYPES
 */
type Props = {
  gauge: Gauge;
  isLoading: boolean;
};

type Reward = {
  token: TokenInfo;
  amount: string;
  value: string;
};

/**
 * PROPS & EMITS
 */
const props = defineProps<Props>();

/**
 * COMPOSABLES
 */
const { t } = useI18n();
const { upToLargeBreakpoint } = useBreakpoints();
const { fNum, toFiat } = useNumbers();
const { getToken } = useTokens();

/**
 * STATE
 */
const columns = ref<ColumnDefinition<Reward>[]>([
  {
    name: t('incentiveToken'),
    id: 'token',
    accessor: 'token',
    Cell: 'tokenColumnCell',
    width: 475,
    noGrow: true,
  },
  {
    name: t('amount'),
    id: 'amount',
    align: 'right',
    width: 150,
    accessor: ({ amount, token }) =>
      `${fNum(amount, FNumFormats.token)} ${token?.symbol}`,
  },
  {
    name: t('value'),
    id: 'value',
    align: 'right',
    width: 150,
    totalsCell: 'totalValueCell',
    accessor: ({ value }) => fNum(value, FNumFormats.fiat),
  },
  {
    name: '',
    id: 'claim',
    accessor: 'claim',
    Cell: 'claimColumnCell',
    totalsCell: 'totalClaimCell',
    width: 150,
  },
]);

/**
 * COMPUTED
 */
const rewardsData = computed((): Reward[] => {
  return props.gauge.rewardTokens
    .map(tokenAddress => {
      const token = getToken(tokenAddress);
      const amount = formatUnits(
        props.gauge.claimableRewards[tokenAddress],
        token?.decimals || 18
      );

      if (
        bnum(amount).isZero() ||
        !token?.address ||
        !token?.symbol ||
        !token?.decimals
      )
        return null;

      return {
        token,
        amount,
        value: token?.address ? toFiat(amount, token?.address) : '0',
      };
    })
    .filter(r => r !== null) as Reward[];
});

const totalRewardValue = computed((): string => {
  return rewardsData.value
    .reduce((acc, reward) => acc.plus(reward.value), bnum('0'))
    .toString();
});
</script>

<template>
  <BalCard
    shadow="lg"
    :square="upToLargeBreakpoint"
    :noBorder="upToLargeBreakpoint"
    noPad
  >
    <BalTable
      :columns="columns"
      :data="rewardsData"
      :isLoading="isLoading"
      skeletonClass="h-64"
      :square="upToLargeBreakpoint"
    >
      <template #tokenColumnCell="{ token }">
        <div class="flex items-center py-4 px-6">
          <BalAsset :iconURI="token?.logoURI" />
          <span class="ml-2">{{ token?.name }}</span>
        </div>
      </template>
      <template #totalValueCell>
        <div class="flex justify-end">
          {{ fNum(totalRewardValue, FNumFormats.fiat) }}
        </div>
      </template>
      <template #totalClaimCell>
        <div class="">
          <ClaimRewardsBtn :gauge="gauge" :fiatValue="totalRewardValue" />
        </div>
      </template>
    </BalTable>
  </BalCard>
</template>
