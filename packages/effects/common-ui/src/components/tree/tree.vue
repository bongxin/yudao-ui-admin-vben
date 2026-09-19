<script setup lang="ts">
import type { TreeProps } from '@vben-core/shadcn-ui';

import { computed, useAttrs } from 'vue';

import { Inbox } from '@vben/icons';
import { $t } from '@vben/locales';

import { treePropsDefaults, VbenTree } from '@vben-core/shadcn-ui';

defineOptions({ inheritAttrs: false });

const props = withDefaults(defineProps<TreeProps>(), treePropsDefaults());
const attrs = useAttrs();

const bindAttrs = computed(() => {
  const rest = { ...attrs };
  delete rest.value;
  delete rest.modelValue;
  delete rest['onUpdate:value'];
  delete rest['onUpdate:modelValue'];
  return rest;
});

const merged = computed(() => ({
  ...bindAttrs.value,
  ...props,
}));

const model = computed(() =>
  Object.prototype.hasOwnProperty.call(attrs, 'value')
    ? attrs.value
    : attrs.modelValue,
);

function onModelUpdate(value: unknown) {
  const updateValue = attrs['onUpdate:value'] as
    | ((next: unknown) => void)
    | undefined;
  const updateModelValue = attrs['onUpdate:modelValue'] as
    | ((next: unknown) => void)
    | undefined;
  updateValue?.(value);
  updateModelValue?.(value);
}
</script>

<template>
  <VbenTree
    v-if="props.treeData?.length > 0"
    v-bind="merged"
    :model-value="model"
    @update:model-value="onModelUpdate"
  >
    <template v-for="(_, key) in $slots" :key="key" #[key]="slotProps">
      <slot :name="key" v-bind="slotProps"> </slot>
    </template>
  </VbenTree>
  <div
    v-else
    class="flex-col-center cursor-pointer rounded-lg border p-10 text-sm font-medium text-muted-foreground"
  >
    <Inbox class="size-10" />
    <div class="mt-1">{{ $t('common.noData') }}</div>
  </div>
</template>
