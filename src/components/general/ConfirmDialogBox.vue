<script setup lang="ts">
import { translateUiText } from '@/utils/i18n';

export interface ConfirmDialogBoxProps {
  title: string;
  description?: string;
  message: string;
  cancelText?: string;
  confirmText?: string;
}
withDefaults(defineProps<ConfirmDialogBoxProps>(), {
  description: translateUiText('Confirmation'),
  cancelText: translateUiText('Cancel'),
  confirmText: translateUiText('Ok'),
});
defineEmits<{
  close: [value: boolean];
}>();
</script>

<template>
  <UModal :title="title" :ui="{ footer: 'justify-end' }" :description="description">
    <template #body>
      <div class="whitespace-pre-wrap">
        {{ message }}
      </div>
    </template>
    <template #footer>
      <UButton
        class="min-w-30"
        :label="cancelText"
        variant="outline"
        color="neutral"
        icon="mdi:close"
        @click="$emit('close', false)"
      />
      <UButton
        class="min-w-30"
        :label="confirmText"
        icon="mdi:check"
        autofocus
        @click="$emit('close', true)"
      />
    </template>
  </UModal>
</template>
