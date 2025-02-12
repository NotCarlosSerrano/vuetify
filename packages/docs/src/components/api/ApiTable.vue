<template>
  <app-sheet>
    <v-table class="api-table" density="comfortable">
      <thead>
        <tr>
          <th
            v-for="header in headers"
            :key="header"
            :class="header"
          >
            <div
              class="text-capitalize"
              v-text="header"
            />
          </th>
        </tr>
      </thead>

      <tbody>
        <template v-for="item in filtered" :key="item.name">
          <slot
            name="row"
            v-bind="{
              ...item,
              props: {
                class: theme.dark ? 'bg-grey-darken-3' : 'bg-grey-lighten-4'
              }
            }"
          />

          <tr v-if="item.description || (DEV && item.source)">
            <td colspan="3" class="text-mono pt-4">
              <template v-if="item.description">
                <app-markdown
                  v-if="localeStore.locale !== 'eo-UY'"
                  :content="item.description"
                  class="mb-0"
                />
                <span v-else>{{ item.description }}</span>
              </template>

              <p v-if="DEV && item.source">
                <strong>source: {{ item.source }}</strong>
              </p>
            </td>
          </tr>
        </template>

        <tr v-if="!filtered.length">
          <td colspan="4" class="text-center text-disabled text-body-2">
            {{ t('search.no-results') }}
          </td>
        </tr>
      </tbody>
    </v-table>
  </app-sheet>
</template>

<script setup lang="ts">
  // Composables
  import { useI18n } from 'vue-i18n'
  import { useTheme } from 'vuetify'

  // Utilities
  import { computed, PropType } from 'vue'

  // Stores
  import { useAppStore } from '@/store/app'
  import { useLocaleStore } from '@/store/locale'

  const props = defineProps({
    headers: {
      type: Array as PropType<string[]>,
      default: () => ([]),
    },
    items: {
      type: Array as PropType<any[]>,
      default: () => [],
    },
  })

  const { current: theme } = useTheme()
  const { t } = useI18n()
  const appStore = useAppStore()
  const localeStore = useLocaleStore()

  const DEV = import.meta.env.DEV

  const filtered = computed(() => {
    if (!appStore.apiSearch) return props.items

    const query = appStore.apiSearch.toLowerCase()

    return props.items.filter((item: any) => {
      return item.name.toLowerCase().includes(query)
    })
  })
</script>
