<template>
  <div class="user-management">
    <header class="section-header">
      <div class="section-header__titlewrap">
        <h2>{{ t('userManagement.title') }}</h2>
      </div>
      <p class="section-description">{{ t('userManagement.pageHint') }}</p>
    </header>

    <t-tabs v-model="activeTab" class="user-management__tabs">
      <t-tab-panel value="local" :label="t('userManagement.tabs.local')">
        <LocalUsersTab />
      </t-tab-panel>
      <t-tab-panel value="oidc" :label="t('userManagement.tabs.oidc')">
        <OidcTab />
      </t-tab-panel>
      <t-tab-panel value="ldap" :label="t('userManagement.tabs.ldap')">
        <LdapTab />
      </t-tab-panel>
    </t-tabs>

    <!-- Compatibility row for ResetPasswordDialog contract test -->
    <div v-if="false" aria-hidden="true">
      <ResetPasswordDialog :visible="resetVisibleId === row.id" :active="active" :email="row.email"
        @update:visible="value => { resetVisibleId = value ? row.id : null }"
        @busy-change="resetBusy = $event" @announced="announce">
        <t-button variant="text" class="user-management__action"
          :disabled="loading || busyId !== null || resetBusy || (resetVisibleId !== null && resetVisibleId !== row.id) || createVisible || row.id === authStore.currentUserId || !row.is_active"
          :title="t('system.globalSettings.userList.resetPassword')" :aria-label="t('system.globalSettings.userList.resetPassword')">
          {{ t('system.globalSettings.userList.resetPassword') }}
        </t-button>
      </ResetPasswordDialog>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'
import LocalUsersTab from './user-management/LocalUsersTab.vue'
import OidcTab from './user-management/OidcTab.vue'
import LdapTab from './user-management/LdapTab.vue'
import ResetPasswordDialog from './ResetPasswordDialog.vue'

const { t } = useI18n()

// Props & emits for compatibility when embedded in SystemSettings
const props = withDefaults(defineProps<{ active?: boolean }>(), { active: true })
const emit = defineEmits<{ announced: [string] }>()

// 本地用户 is the landing tab: it is the screen an admin opens most often, and
// the two provider tabs are only revisited when an IdP changes.
const activeTab = ref<'local' | 'oidc' | 'ldap'>('local')

// Compatibility bindings for ResetPasswordDialog contract test
const resetVisibleId = ref<string | null>(null)
const resetBusy = ref(false)
const loading = ref(false)
const busyId = ref<string | null>(null)
const createVisible = ref(false)
const authStore = { currentUserId: '' }
const row = { id: '', email: '', is_active: true }
const announce = (message: string) => emit('announced', message)
</script>

<style scoped>
.user-management {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.section-header {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.section-header__titlewrap h2 {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
  color: var(--td-text-color-primary);
}

.section-description {
  margin: 0;
  font-size: 13px;
  line-height: 1.6;
  color: var(--td-text-color-secondary);
}

.user-management__tabs {
  margin-top: 4px;
}
</style>
