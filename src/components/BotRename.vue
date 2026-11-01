<script setup lang="ts">
import type { BotDescriptor } from '@/types';
const props = defineProps<{
  bot: BotDescriptor;
}>();
const emit = defineEmits<{ cancelled: []; saved: [] }>();

const botStore = useBotStore();
const newName = ref<string>('');

onMounted(() => {
  newName.value = props.bot.botName;
});

const save = () => {
  botStore.updateBot(props.bot.botId, {
    botName: newName.value,
  });

  emit('saved');
};
</script>

<template>
  <form class="flex w-full gap-2" @submit.prevent="save">
    <UInput v-model="newName" class="w-full" :placeholder="$t('Bot name')" autofocus />

    <div class="flex gap-1">
      <UButton type="submit" color="neutral" :title="$t('Save')" icon="mdi:check" />

      <UButton color="neutral" :title="$t('Cancel')" @click="$emit('cancelled')" icon="mdi:close">
      </UButton>
    </div>
  </form>
</template>
