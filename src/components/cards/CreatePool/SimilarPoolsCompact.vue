<script lang="ts" setup>
import usePoolCreation from '@/composables/pools/usePoolCreation';
import useNumbers, { FNumFormats } from '@/composables/useNumbers';
import { useTokens } from '@/providers/tokens.provider';
import { Pool } from '@/services/pool/types';
import { networkSlug } from '@/composables/useNetwork';

/**
 * COMPOSABLES
 */
const { similarPools } = usePoolCreation();
const { getToken } = useTokens();
const { fNum } = useNumbers();
/**
 * FUNCTIONS
 */
function getPoolLabel(pool: Pool) {
  const tokensString = pool.tokens
    .map(
      t =>
        `${getToken(t.address)?.symbol} ${fNum(
          t.weight || '0',
          FNumFormats.percent
        )}`
    )
    .join(', ');
  return `${tokensString} (${fNum(pool.swapFee, FNumFormats.percent)} fee)`;
}
</script>

<template>
  <BalCard noPad shadow="none">
    <BalStack
      spacing="sm"
      align="center"
      horizontal
      class="p-2 px-3 text-orange-500 border-b dark:border-gray-600"
    >
      <BalIcon class="mt-1" name="alert-circle" size="md" />
      <h6>{{ $t('createAPool.similarPoolsExist') }}</h6>
    </BalStack>
    <BalStack vertical spacing="sm" class="p-4">
      <BalLink
        v-for="pool in similarPools"
        :key="`similarpool-${pool.id}`"
        target="_blank"
        :href="`/#/${networkSlug}/pool/${pool.id}`"
      >
        <span class="text-sm">{{ getPoolLabel(pool) }}</span>
      </BalLink>
    </BalStack>
  </BalCard>
</template>
