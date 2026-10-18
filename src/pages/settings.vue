<script setup lang="ts">
import { FtWsMessageTypes } from '@/types/wsMessageTypes';

import { translateUiText } from '@/utils/i18n';

const settingsStore = useSettingsStore();
const colorStore = useColorStore();
const layoutStore = useLayoutStore();

const timezoneOptions = ['UTC', Intl.DateTimeFormat().resolvedOptions().timeZone];
const openTradesOptions = [
  { value: OpenTradeVizOptions.showPill, text: translateUiText('Show pill in icon') },
  { value: OpenTradeVizOptions.asTitle, text: translateUiText('Show in title') },
  {
    value: OpenTradeVizOptions.noOpenTrades,
    text: translateUiText("Don't show open trades in header"),
  },
];
const colorPreferenceOptions = [
  { value: ColorPreferences.GREEN_UP, text: translateUiText('Green Up/Red Down') },
  { value: ColorPreferences.RED_UP, text: translateUiText('Red Up/Green Down') },
];

const resetDynamicLayout = () => {
  layoutStore.resetTradingLayout();
  layoutStore.resetDashboardLayout();
  showAlert(translateUiText('Layouts have been reset.'));
};
</script>

<template>
  <UCard class="mx-auto mt-3 p-4 max-w-4xl">
    <template #header><span class="text-2xl font-bold">{{ $t('FreqUI Settings') }}</span></template>
    <div class="flex flex-col gap-4 text-start dark:text-neutral-300">
      <p class="text-left">{{ $t('UI Version') }}: {{ settingsStore.uiVersion }}</p>

      <div class="border border-neutral-400 rounded-sm p-4 space-y-4">
        <h4 class="text-xl font-semibold">{{ $t('UI settings') }}</h4>

        <BaseCheckbox v-model="layoutStore.layoutLocked" class="space-y-1">
          {{ $t('Lock dynamic layouts') }}
          <template #hint>
            {{ $t('Lock dynamic layouts, so they cannot move anymore. Can also be set from the navbar at the top.') }}
          </template>
        </BaseCheckbox>

        <div class="flex flex-row items-center gap-2 space-y-2">
          <UButton color="neutral" size="md" class="mb-0" @click="resetDynamicLayout"
            >{{ $t('Reset layout') }}</UButton
          >
          <small class="text-sm block text-neutral-600 dark:text-neutral-400"
            >{{ $t('Reset dynamic layouts to how they were.') }}</small
          >
        </div>

        <USeparator />

        <div class="space-y-1">
          <label class="block text-sm">{{ $t('Show open trades in header') }}</label>
          <USelect
            v-model="settingsStore.openTradesInTitle"
            :items="openTradesOptions"
            label-key="text"
            value-key="value"
            class="w-full"
          />
          <small class="text-sm text-neutral-600 dark:text-neutral-400"
            >{{ $t('Decide if open trades should be visualized') }}</small
          >
        </div>

        <div class="space-y-1">
          <label class="block text-sm">{{ $t('UTC Timezone') }}</label>
          <USelect v-model="settingsStore.timezone" :items="timezoneOptions" class="w-full" />
          <small class="text-sm text-neutral-600 dark:text-neutral-400"
            >{{ $t('Select timezone (UTC is recommended as exchanges usually work in UTC)') }}</small
          >
        </div>

        <BaseCheckbox v-model="settingsStore.backgroundSync" class="space-y-1">
          {{ $t('Background sync') }}
          <template #hint>{{ $t('Keep background sync running while other bots are selected.') }}</template>
        </BaseCheckbox>

        <BaseCheckbox v-model="settingsStore.confirmDialog" class="space-y-1">
          {{ $t('Show Confirm Dialog for Trade Exits') }}
          <template #hint
            >{{ $t('Use confirmation dialogs when force-exiting a trade.') }}<br />
            This will also show <i-mdi-run-fast class="text-yellow-300 inline" />
            <i-mdi-alert class="text-yellow-300 inline" />
            in the title bar.
          </template>
        </BaseCheckbox>

        <BaseCheckbox v-model="settingsStore.multiPaneButtonsShowText" class="space-y-1">
          {{ $t('Show Text on Multi Pane Buttons') }}
          <template #hint
            >{{ $t('Show text on multi pane buttons. If disabled, only shows images.') }}</template
          >
        </BaseCheckbox>
      </div>

      <div class="border border-neutral-400 rounded-sm p-4 space-y-4">
        <h4 class="text-lg font-semibold">{{ $t('Chart settings') }}</h4>

        <div class="space-y-1">
          <label class="block text-sm">{{ $t('Chart scale Side') }}</label>
          <URadioGroup
            v-model="settingsStore.chartLabelSide"
            :items="[
              { label: $t('Left'), value: 'left' },
              { label: $t('Right'), value: 'right' },
            ]"
            orientation="horizontal"
          />
          <small class="text-sm text-neutral-600 dark:text-neutral-400">
            {{ $t('Should the scale be displayed on the right or left?') }}
          </small>
        </div>

        <BaseCheckbox v-model="settingsStore.useHeikinAshiCandles" class="space-y-1">
          {{ $t('Use Heikin Ashi candles') }}
          <template #hint>{{ $t('Use Heikin Ashi candles in your charts') }}</template>
        </BaseCheckbox>

        <BaseCheckbox v-model="settingsStore.useReducedPairCalls" class="space-y-1">
          {{ $t('Only request necessary columns') }}
          <template #hint
            >{{ $t('Can reduce the transfer size for large dataframes. May require additional calls if the plot config changes.') }}</template
          >
        </BaseCheckbox>

        <div>
          <p>{{ $t('Default number of candles to display (defaults to 250)') }}</p>
          <div class="flex flex-row gap-5 w-full items-center">
            <USlider
              v-model="settingsStore.chartDefaultCandleCount"
              class="flex-1"
              :step="50"
              :min="100"
              :max="2000"
            />
            <UInputNumber
              v-model="settingsStore.chartDefaultCandleCount"
              :step="50"
              :min="100"
              :max="2000"
              size="sm"
            />
          </div>
        </div>

        <div class="space-y-1">
          <label class="block">{{ $t('Candle Color Preference') }}</label>
          <div class="flex flex-row gap-5 items-center">
            <URadioGroup
              v-model="colorStore.colorPreference"
              :items="colorPreferenceOptions"
              label-key="text"
              value-key="value"
              orientation="horizontal"
            >
              <template #label="{ item }">
                <div class="flex items-center">
                  <span class="mr-2">{{ item.text }}</span>
                  <UIcon
                    name="mdi:arrow-up-thin"
                    :color="
                      item.value === ColorPreferences.GREEN_UP
                        ? colorStore.colorProfit
                        : colorStore.colorLoss
                    "
                    class="-ml-2 size-5"
                  />
                  <UIcon
                    name="mdi:arrow-down-thin"
                    :color="
                      item.value === ColorPreferences.GREEN_UP
                        ? colorStore.colorLoss
                        : colorStore.colorProfit
                    "
                    class="-ml-2 size-5"
                  />
                </div>
              </template>
            </URadioGroup>
          </div>
        </div>
      </div>

      <div class="border rounded-sm border-neutral-400 p-4 space-y-4">
        <h4 class="text-lg font-semibold">{{ $t('Notification Settings') }}</h4>
        <div class="space-y-2">
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.entryFill]">
            {{ $t('Entry notifications') }}
          </BaseCheckbox>
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.exitFill]">
            {{ $t('Exit notifications') }}
          </BaseCheckbox>
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.entryCancel]">
            {{ $t('Entry Cancel notifications') }}
          </BaseCheckbox>
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.exitCancel]">
            {{ $t('Exit Cancel notifications') }}
          </BaseCheckbox>
        </div>
      </div>

      <div class="border rounded-sm border-neutral-400 p-4 space-y-4">
        <h4 class="text-lg font-semibold">{{ $t('Backtesting settings') }}</h4>
        <div>
          <label for="backtestMetrics" class="block">{{ $t('Backtesting metrics') }}</label>
          <USelectMenu
            multiple
            id="backtestMetrics"
            v-model="settingsStore.backtestAdditionalMetrics"
            :items="availableBacktestMetrics"
            label-key="header"
            value-key="field"
            class="w-full"
            display="chip"
          />
          <small class="text-sm text-neutral-600 dark:text-neutral-400"
            >{{ $t('Select which metrics should be shown on a per pair / tag basis.') }}</small
          >
        </div>
      </div>
    </div>
  </UCard>
</template>
