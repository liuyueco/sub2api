<template>
  <main class="home-shell min-h-screen overflow-hidden bg-[#03060b] text-white">
    <section
      class="home-hero relative flex min-h-screen flex-col"
    >
      <div class="home-grid pointer-events-none absolute inset-0"></div>
      <div class="home-beam pointer-events-none absolute inset-0"></div>

      <header class="fixed inset-x-0 top-0 z-50 px-4 pt-4 sm:px-6">
        <nav
          class="mx-auto flex h-16 max-w-6xl items-center justify-between rounded-full border border-white/10 bg-white/[0.045] px-4 shadow-[0_22px_90px_rgba(0,0,0,0.45),0_0_46px_rgba(56,163,250,0.10),inset_0_1px_0_rgba(255,255,255,0.08)] backdrop-blur-2xl sm:px-6"
        >
          <router-link to="/home" class="flex min-w-0 items-center gap-2.5" aria-label="titanrouter home">
            <img :src="appStore.siteLogo || '/logo.png'" alt="" class="h-9 w-9 shrink-0 object-contain" aria-hidden="true" />
            <span class="inline-flex min-w-0 items-baseline gap-1 truncate text-xl font-black leading-none">
              <span class="text-white">titan</span>
              <span class="brand-gradient">router</span>
            </span>
          </router-link>

          <div class="hidden items-center gap-8 text-sm font-medium text-white/62 md:flex">
            <a
              v-for="item in copy.navItems"
              :key="item.href"
              :href="item.href"
              class="transition hover:text-[#9bd2ff]"
            >
              {{ item.label }}
            </a>
          </div>

          <div class="flex items-center gap-1 sm:gap-2">
            <LocaleSwitcher />
            <router-link
              :to="isAuthenticated ? dashboardPath : '/login'"
              class="inline-flex h-10 items-center justify-center rounded-lg border border-white/15 bg-[#38a3fa]/90 px-4 text-sm font-black text-white shadow-[0_0_34px_rgba(56,163,250,0.30),inset_0_1px_0_rgba(255,255,255,0.24)] transition hover:bg-[#67b9ff]"
            >
              {{ isAuthenticated ? copy.auth.dashboard : copy.auth.login }}
            </router-link>
          </div>
        </nav>
      </header>

      <div class="relative z-10 mx-auto flex w-full max-w-6xl flex-1 flex-col items-center justify-center px-5 pb-14 pt-32 text-center sm:pt-36 lg:pb-16">
        <h1 class="max-w-5xl text-5xl font-black leading-[1.04] text-white sm:text-6xl lg:text-7xl">
          <span class="block">
            {{ siteDisplayName }} <span class="text-emerald-400">&gt;</span> AI Gateway
          </span>
          <span class="mt-4 block bg-gradient-to-r from-[#38a3fa] to-[#267df2] bg-clip-text text-transparent">
            {{ copy.heroTitle }} {{ copy.heroTitleSuffix }}
          </span>
        </h1>

        <p class="mt-7 max-w-2xl text-base leading-8 text-slate-300/78 sm:text-lg">
          {{ copy.heroSubtitle }}
        </p>

        <div class="mt-10 flex flex-col items-center gap-3 sm:flex-row">
          <router-link
            :to="isAuthenticated ? dashboardPath : '/register'"
            class="inline-flex h-14 min-w-44 items-center justify-center gap-2 rounded-full border border-[#6bbdff]/35 bg-[#38a3fa]/90 px-8 text-base font-black text-white shadow-[0_18px_54px_rgba(56,163,250,0.30)] transition hover:-translate-y-0.5 hover:bg-[#67b9ff]"
          >
            <Icon name="bolt" size="md" />
            {{ copy.cta.primary }}
            <Icon name="arrowRight" size="sm" />
          </router-link>
          <button
            type="button"
            class="inline-flex h-14 min-w-44 items-center justify-center gap-2 rounded-full border border-[#273449] bg-[#0b1018]/90 px-8 text-base font-black text-white shadow-[0_18px_54px_rgba(0,0,0,0.28)] transition hover:-translate-y-0.5 hover:border-[#38a3fa]/45 hover:bg-[#101827]"
            @click="openGuideModal"
          >
            <Icon name="terminal" size="md" class="text-[#38a3fa]" />
            {{ copy.cta.guide }}
          </button>
        </div>

        <div class="mt-10 grid w-full max-w-5xl gap-4 md:grid-cols-3">
          <article
            v-for="feature in copy.heroFeatures"
            :key="feature.title"
            class="hero-feature-card flex items-start gap-4 rounded-lg border border-[#273449] bg-[#0b1018]/88 p-5 text-left shadow-[0_20px_70px_rgba(0,0,0,0.28)] transition hover:border-[#38a3fa]/38 hover:bg-[#101827]"
          >
            <Icon :name="feature.icon" size="md" class="mt-0.5 shrink-0 text-[#38a3fa]" />
            <span>
              <h2 class="text-base font-bold text-white">{{ feature.title }}</h2>
              <p class="mt-1 text-sm leading-6 text-slate-400">{{ feature.desc }}</p>
            </span>
          </article>
        </div>

        <div class="mt-20 w-full max-w-4xl">
          <p class="text-sm text-slate-400">{{ copy.metricsIntro }}</p>
          <dl class="mt-6 grid gap-6 sm:grid-cols-4">
            <div v-for="metric in copy.metrics" :key="metric.label" class="text-center">
              <dt class="text-2xl font-black text-[#38a3fa] sm:text-3xl">{{ metric.value }}</dt>
              <dd class="mt-1 text-sm text-slate-500">{{ metric.label }}</dd>
            </div>
          </dl>
        </div>
      </div>
    </section>

    <Teleport to="body">
      <div
        v-if="showGuideModal"
        class="fixed inset-0 z-[100] flex items-center justify-center bg-black/62 px-4 py-6 backdrop-blur-md"
        role="dialog"
        aria-modal="true"
        @click.self="closeGuideModal"
      >
        <section class="guide-modal max-h-[92vh] w-full max-w-4xl overflow-y-auto rounded-2xl border border-gray-200 bg-white p-5 text-gray-900 shadow-[0_30px_120px_rgba(0,0,0,0.72)] sm:p-7">
          <div class="flex items-start justify-between gap-4">
            <div>
              <div class="flex flex-wrap items-center gap-2 text-sm font-semibold text-[#38a3fa]">
                <span>{{ copy.guide.eyebrow }}</span>
                <span class="rounded-full border border-primary-200 bg-primary-50 px-3 py-1 text-xs text-primary-700">
                  {{ copy.guide.badge }}
                </span>
              </div>
              <h2 class="mt-2 text-xl font-black text-gray-900 sm:text-2xl">{{ copy.guide.title }}</h2>
            </div>
            <button
              type="button"
              class="rounded-lg p-2 text-gray-500 transition hover:bg-gray-100 hover:text-gray-900"
              :aria-label="copy.guide.close"
              @click="closeGuideModal"
            >
              <Icon name="x" size="md" :stroke-width="2" />
            </button>
          </div>

          <div class="mt-5 flex flex-wrap gap-2">
            <button
              v-for="tool in copy.guide.tools"
              :key="tool.name"
              type="button"
              class="inline-flex h-9 items-center gap-2 rounded-full border px-3 text-sm font-semibold transition"
              :class="selectedGuideTool === tool.name
                ? 'border-[#38a3fa] bg-[#38a3fa] text-white'
                : 'border-gray-200 bg-white text-gray-700 hover:border-primary-300 hover:bg-gray-50'"
              @click="selectGuideTool(tool.name)"
            >
              <span>{{ tool.icon }}</span>
              {{ tool.name }}
            </button>
          </div>

          <div class="mt-5 flex flex-col gap-3 text-sm sm:flex-row sm:items-center">
            <label class="text-gray-500" for="guide-model">{{ copy.guide.modelLabel }}</label>
            <select
              id="guide-model"
              v-model="selectedGuideModel"
              class="h-10 rounded-lg border border-gray-200 bg-white px-3 font-medium text-gray-900 outline-none transition focus:border-primary-500 focus:ring-2 focus:ring-primary-500/20"
            >
              <option v-for="model in copy.guide.models" :key="model" :value="model">{{ model }}</option>
            </select>
            <span class="text-gray-500">{{ copy.guide.modelHint }}</span>
          </div>

          <div v-if="!isIntegratedGuide" class="mt-4 flex flex-wrap items-center justify-between gap-3">
            <div class="inline-flex rounded-lg border border-gray-200 bg-gray-50 p-1">
              <button
                v-for="os in copy.guide.osTabs"
                :key="os"
                type="button"
                class="h-9 rounded-md px-4 text-sm font-semibold transition"
                :class="selectedGuideOs === os ? 'bg-[#38a3fa] text-white' : 'text-gray-500 hover:bg-white hover:text-gray-900'"
                @click="selectedGuideOs = os"
              >
                {{ os }}
              </button>
            </div>
            <span class="text-sm text-gray-500">{{ copy.guide.detected }} {{ selectedGuideOs }}</span>
          </div>

          <p class="mt-4 text-sm leading-6 text-gray-600">{{ guideInstruction }}</p>

          <div v-if="isCcSwitchGuide" class="mt-3 rounded-lg border border-dashed border-primary-300 bg-primary-50/40 p-5">
            <h3 class="text-base font-black text-gray-900">{{ copy.guide.ccSwitch.title }}</h3>

            <div class="mt-4 flex flex-wrap items-center gap-3">
              <span class="text-sm text-gray-500">{{ copy.guide.ccSwitch.importLabel }}</span>
              <div class="inline-flex rounded-lg border border-gray-200 bg-white p-1">
                <button
                  v-for="target in ccSwitchTargets"
                  :key="target.value"
                  type="button"
                  class="h-9 rounded-md px-4 text-sm font-semibold transition"
                  :class="selectedCcSwitchTarget === target.value ? 'bg-[#38a3fa] text-white' : 'text-gray-500 hover:bg-gray-50 hover:text-gray-900'"
                  @click="selectedCcSwitchTarget = target.value"
                >
                  {{ target.label }}
                </button>
              </div>
              <span class="text-xs text-primary-700 sm:ml-auto">{{ copy.guide.ccSwitch.subModel }}</span>
            </div>

            <div class="mt-4 flex flex-wrap items-center gap-2">
              <button
                type="button"
                class="inline-flex h-10 items-center justify-center gap-2 rounded-lg bg-[#38a3fa] px-4 text-sm font-black text-white transition hover:bg-[#67b9ff]"
                @click="openCcSwitchImport"
              >
                <Icon name="bolt" size="sm" />
                {{ copy.guide.ccSwitch.open }}
              </button>
              <span class="px-1 text-sm text-gray-500">{{ copy.guide.ccSwitch.notInstalled }}</span>
              <a
                v-for="download in ccSwitchDownloads"
                :key="download.label"
                :href="download.href"
                target="_blank"
                rel="noopener noreferrer"
                class="inline-flex h-9 items-center justify-center gap-2 rounded-lg border border-gray-200 bg-white px-3 text-sm font-semibold text-gray-700 transition hover:border-primary-300 hover:bg-gray-50 hover:text-gray-900"
              >
                <Icon name="download" size="sm" />
                {{ download.label }}
              </a>
            </div>

            <a
              :href="ccSwitchProjectUrl"
              target="_blank"
              rel="noopener noreferrer"
              class="mt-4 inline-flex text-sm font-semibold text-primary-700 transition hover:text-primary-600"
            >
              {{ copy.guide.ccSwitch.project }}: farion1231/cc-switch
            </a>

            <div class="mt-4 rounded-md border border-primary-100 bg-white/70 px-3 py-2 text-sm text-gray-600">
              {{ copy.guide.ccSwitch.notice }}
            </div>
          </div>

          <div v-else-if="isCursorGuide" class="mt-3 rounded-lg border border-dashed border-primary-300 bg-primary-50/40 p-5">
            <h3 class="text-base font-black text-gray-900">{{ copy.guide.cursor.title }}</h3>
            <ol class="mt-3 space-y-2 pl-5 text-sm leading-6 text-gray-600">
              <li v-for="step in copy.guide.cursor.steps" :key="step" class="list-decimal">{{ step }}</li>
            </ol>

            <div class="mt-5 space-y-3 border-t border-primary-100 pt-4">
              <div v-for="field in cursorConfigFields" :key="field.label" class="grid gap-2 sm:grid-cols-[4rem_1fr_auto] sm:items-center">
                <span class="text-sm text-gray-500">{{ field.label }}</span>
                <code class="min-w-0 overflow-x-auto rounded-md border border-gray-200 bg-white px-3 py-2 text-xs text-gray-900">
                  {{ field.value }}
                </code>
                <button
                  type="button"
                  class="inline-flex h-9 w-9 items-center justify-center rounded-md bg-[#38a3fa] text-white transition hover:bg-[#67b9ff]"
                  :title="copiedCursorField === field.key ? copy.guide.copied : copy.guide.copy"
                  @click="copyCursorConfigValue(field.key, field.value)"
                >
                  <Icon name="copy" size="sm" />
                </button>
              </div>
            </div>

            <div class="mt-4 rounded-md border border-primary-100 bg-white/70 px-3 py-2 text-sm text-gray-600">
              {{ copy.guide.cursor.notice }}
            </div>
          </div>

          <div v-else class="relative mt-3 overflow-hidden rounded-lg border border-gray-200 bg-gray-950">
            <pre class="max-h-48 overflow-auto p-4 pr-14 text-left text-xs leading-5 text-slate-100"><code>{{ guideScript }}</code></pre>
            <button
              type="button"
              class="absolute right-3 top-3 rounded-md border border-dark-600 bg-dark-800 p-2 text-slate-300 shadow-sm transition hover:bg-dark-700 hover:text-white"
              :title="copiedGuideScript ? copy.guide.copied : copy.guide.copy"
              @click="copyGuideScript"
            >
              <Icon name="copy" size="sm" />
            </button>
          </div>

          <div v-if="!isIntegratedGuide" class="mt-4 flex flex-col gap-3 sm:flex-row sm:items-center">
            <button
              type="button"
              class="inline-flex h-10 items-center justify-center gap-2 rounded-lg border border-gray-200 bg-white px-4 text-sm font-semibold text-gray-700 transition hover:border-primary-300 hover:bg-gray-50 hover:text-gray-900"
              @click="downloadGuideScript"
            >
              <Icon name="download" size="sm" />
              {{ copy.guide.download }}
            </button>
            <span class="text-sm text-gray-500">{{ copy.guide.or }}</span>
            <code class="rounded-md border border-gray-200 bg-gray-50 px-3 py-2 text-xs text-gray-700">
              {{ guideRunCommand }}
            </code>
          </div>

          <p class="mt-4 text-sm leading-6 text-gray-500">{{ copy.guide.tip }}</p>

          <div class="mt-4 rounded-lg border border-primary-200 bg-primary-50 px-4 py-3 text-sm leading-6 text-primary-900">
            {{ copy.guide.keyNotice }}
          </div>

        </section>
      </div>
    </Teleport>

    <section id="docs" class="section-dark border-t border-[#38a3fa]/10 px-5 py-24">
      <div class="mx-auto grid max-w-6xl gap-8 lg:grid-cols-[0.9fr_1.1fr] lg:items-center">
        <div>
          <p class="text-sm font-semibold uppercase tracking-[0.24em] text-[#38a3fa]">{{ copy.docs.eyebrow }}</p>
          <h2 class="mt-4 text-3xl font-black text-white sm:text-4xl">{{ copy.docs.title }}</h2>
          <p class="mt-5 max-w-xl leading-8 text-slate-400">
            {{ copy.docs.desc }}
          </p>
        </div>

        <div class="grid gap-4 sm:grid-cols-2">
          <article
            v-for="item in copy.capabilityCards"
            :key="item.title"
            class="rounded-lg border border-[#38a3fa]/10 bg-white/[0.035] p-5 shadow-[0_20px_70px_rgba(0,0,0,0.20)] backdrop-blur"
          >
            <Icon :name="item.icon" size="lg" class="text-[#38a3fa]" />
            <h3 class="mt-5 text-lg font-semibold text-white">{{ item.title }}</h3>
            <p class="mt-2 text-sm leading-6 text-slate-400">{{ item.desc }}</p>
          </article>
        </div>
      </div>
    </section>

    <section id="pricing" class="section-darker border-t border-[#38a3fa]/10 px-5 py-24">
      <div class="mx-auto max-w-6xl">
        <div class="max-w-2xl">
          <p class="text-sm font-semibold uppercase tracking-[0.24em] text-[#38a3fa]">{{ copy.pricing.eyebrow }}</p>
          <h2 class="mt-4 text-3xl font-black text-white sm:text-4xl">{{ copy.pricing.title }}</h2>
        </div>

        <div class="mt-10 grid gap-4 lg:grid-cols-3">
          <article
            v-for="plan in displayPlans"
            :key="plan.id"
            class="rounded-lg border border-[#38a3fa]/10 bg-white/[0.04] p-6 shadow-[0_20px_80px_rgba(0,0,0,0.24)] backdrop-blur transition hover:border-[#38a3fa]/28"
          >
            <div class="flex items-center justify-between gap-3">
              <h3 class="text-xl font-bold text-white">{{ plan.name }}</h3>
              <span v-if="plan.badge" class="rounded-full bg-[#38a3fa]/15 px-3 py-1 text-xs font-semibold text-[#9bd2ff]">{{ plan.badge }}</span>
            </div>
            <p class="mt-4 text-sm leading-6 text-slate-400">{{ plan.desc }}</p>
            <div class="mt-6 flex items-end gap-2">
              <span v-if="plan.originalPrice" class="pb-1 text-sm text-slate-500 line-through">
                {{ plan.originalPrice }}
              </span>
              <span class="text-3xl font-black text-white">{{ plan.price }}</span>
              <span v-if="plan.suffix" class="pb-1 text-sm text-slate-500">{{ plan.suffix }}</span>
            </div>
            <dl v-if="plan.meta.length" class="mt-5 grid grid-cols-2 gap-3 rounded-lg bg-black/20 p-3 text-xs">
              <div v-for="item in plan.meta" :key="item.label">
                <dt class="text-slate-500">{{ item.label }}</dt>
                <dd class="mt-1 font-semibold text-slate-200">{{ item.value }}</dd>
              </div>
            </dl>
            <ul v-if="plan.features.length" class="mt-5 space-y-2 text-sm text-slate-400">
              <li v-for="feature in plan.features" :key="feature" class="flex gap-2">
                <Icon name="check" size="sm" class="mt-0.5 shrink-0 text-[#38a3fa]" />
                <span>{{ feature }}</span>
              </li>
            </ul>
            <router-link
              :to="isAuthenticated ? '/purchase?tab=subscription' : '/login'"
              class="mt-6 inline-flex h-11 w-full items-center justify-center rounded-lg bg-[#38a3fa] text-sm font-black text-[#04111d] transition hover:bg-[#67b9ff]"
            >
              {{ isAuthenticated ? copy.cta.subscribe : copy.cta.loginToSubscribe }}
            </router-link>
          </article>
        </div>
      </div>
    </section>

    <section id="reviews" class="section-dark border-t border-[#38a3fa]/10 px-5 py-24">
      <div class="mx-auto max-w-6xl">
        <p class="text-sm font-semibold uppercase tracking-[0.24em] text-[#38a3fa]">{{ copy.reviews.eyebrow }}</p>
        <div class="mt-8 grid gap-4 md:grid-cols-3">
          <blockquote
            v-for="review in copy.reviews.items"
            :key="review.name"
            class="rounded-lg border border-[#38a3fa]/10 bg-white/[0.035] p-6"
          >
            <p class="leading-7 text-slate-300">{{ review.quote }}</p>
            <footer class="mt-6 text-sm font-semibold text-white">{{ review.name }}</footer>
          </blockquote>
        </div>
      </div>
    </section>

    <section id="faq" class="section-darker border-t border-[#38a3fa]/10 px-5 py-24">
      <div class="mx-auto max-w-4xl">
        <h2 class="text-center text-4xl font-black text-white sm:text-5xl lg:text-6xl">
          {{ copy.faq.titlePrefix }}
          <span class="bg-gradient-to-r from-[#38a3fa] via-[#75c1ff] to-[#38a3fa] bg-clip-text text-transparent">
            {{ copy.faq.titleAccent }}
          </span>
        </h2>
        <div class="mt-12 space-y-6">
          <details
            v-for="item in copy.faq.items"
            :key="item.question"
            class="group rounded-lg border border-[#38a3fa]/10 bg-white/[0.04] p-6 shadow-[0_18px_60px_rgba(0,0,0,0.24)]"
            open
          >
            <summary class="flex cursor-pointer list-none items-center justify-between gap-4 text-left font-semibold text-white">
              {{ item.question }}
              <span class="shrink-0 text-lg font-semibold text-[#38a3fa] transition group-open:rotate-45">+</span>
            </summary>
            <p class="mt-5 leading-8 text-slate-400">{{ item.answer }}</p>
          </details>
        </div>
      </div>
    </section>

    <footer id="contact" class="border-t border-[#38a3fa]/10 bg-black px-5 py-10">
      <div class="mx-auto flex max-w-6xl flex-col gap-4 text-sm text-slate-500 sm:flex-row sm:items-center sm:justify-between">
        <p>© {{ currentYear }} titanrouter. All rights reserved.</p>
        <div class="flex gap-5">
          <a v-if="docUrl" :href="docUrl" target="_blank" rel="noopener noreferrer" class="transition hover:text-white">{{ copy.footer.docs }}</a>
          <router-link to="/login" class="transition hover:text-white">{{ copy.auth.login }}</router-link>
          <router-link to="/register" class="transition hover:text-white">{{ copy.auth.register }}</router-link>
        </div>
      </div>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import { apiClient } from '@/api/client'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'
import type { SubscriptionPlan } from '@/types/payment'
import { buildCcSwitchImportDeeplink, type CcSwitchClientType } from '@/utils/ccswitchImport'
import type { GroupPlatform } from '@/types'

type IconName = InstanceType<typeof Icon>['$props']['name']

interface HomeCopy {
  navItems: Array<{ label: string; href: string }>
  auth: { login: string; register: string; dashboard: string }
  eyebrow: string
  heroTitle: string
  heroTitleSuffix: string
  heroSubtitle: string
  cta: { primary: string; secondary: string; guide: string; start: string; subscribe: string; loginToSubscribe: string }
  heroFeatures: Array<{ icon: IconName; title: string; desc: string }>
  metricsIntro: string
  metrics: Array<{ value: string; label: string }>
  guide: {
    eyebrow: string
    badge: string
    title: string
    close: string
    tools: Array<{ name: string; icon: string }>
    modelLabel: string
    models: string[]
    modelHint: string
    osTabs: string[]
    detected: string
    instruction: string
    copy: string
    copied: string
    download: string
    or: string
    tip: string
    keyNotice: string
    ccSwitch: {
      title: string
      description: string
      importLabel: string
      targets: { claude: string; codex: string }
      subModel: string
      open: string
      notInstalled: string
      project: string
      notice: string
    }
    cursor: {
      description: string
      title: string
      steps: string[]
      notice: string
    }
  }
  docs: { eyebrow: string; title: string; desc: string }
  capabilityCards: Array<{ icon: IconName; title: string; desc: string }>
  pricing: { eyebrow: string; title: string }
  plans: Array<{ name: string; price: string; desc: string; badge: string }>
  planLabels: { days: string; rate: string; daily: string; weekly: string; monthly: string; quota: string; unlimited: string }
  reviews: { eyebrow: string; items: Array<{ name: string; quote: string }> }
  faq: { titlePrefix: string; titleAccent: string; items: Array<{ question: string; answer: string }> }
  footer: { docs: string }
}

interface DisplayPlan {
  id: string
  name: string
  desc: string
  badge: string
  price: string
  originalPrice: string
  suffix: string
  features: string[]
  meta: Array<{ label: string; value: string }>
}

const authStore = useAuthStore()
const appStore = useAppStore()
const { locale } = useI18n()
const publicPlans = ref<SubscriptionPlan[]>([])
const publicPlansLoaded = ref(false)
const showGuideModal = ref(false)
const selectedGuideTool = ref('CC Switch')
const selectedGuideOs = ref('macOS')
const selectedGuideModel = ref('claude-opus-4-7')
const selectedCcSwitchTarget = ref<'claude' | 'codex'>('claude')
const copiedGuideScript = ref(false)
const copiedCursorField = ref('')

const isAuthenticated = computed(() => authStore.isAuthenticated)
const dashboardPath = computed(() => (authStore.isAdmin ? '/admin/dashboard' : '/dashboard'))
const docUrl = computed(() => appStore.cachedPublicSettings?.doc_url || appStore.docUrl || '')
const siteDisplayName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'titanrouter')
const currentYear = computed(() => new Date().getFullYear())
const copy = computed<HomeCopy>(() => homeCopies[locale.value === 'zh' ? 'zh' : 'en'])
const isCcSwitchGuide = computed(() => selectedGuideTool.value === 'CC Switch')
const isClaudeCodeGuide = computed(() => selectedGuideTool.value === 'Claude Code')
const isCodexGuide = computed(() => selectedGuideTool.value === 'Codex')
const isCursorGuide = computed(() => selectedGuideTool.value === 'Cursor')
const isHermesGuide = computed(() => selectedGuideTool.value === 'Hermes')
const isOpenClawGuide = computed(() => selectedGuideTool.value === 'OpenClaw')
const isIntegratedGuide = computed(() => isCcSwitchGuide.value || isCursorGuide.value)
const isProfileSetupGuide = computed(() => isClaudeCodeGuide.value || isCodexGuide.value || isHermesGuide.value || isOpenClawGuide.value)
const guideInstruction = computed(() => {
  if (isCcSwitchGuide.value) return copy.value.guide.ccSwitch.description
  if (isCursorGuide.value) return copy.value.guide.cursor.description
  return copy.value.guide.instruction
})
const cursorBaseUrl = computed(() => `${window.location.origin.replace(/\/+$/, '')}/v1`)
const cursorConfigFields = computed(() => [
  { key: 'baseUrl', label: 'Base URL', value: cursorBaseUrl.value },
  { key: 'apiKey', label: 'API Key', value: 'sk-your-key-here' },
  { key: 'model', label: 'Model', value: selectedGuideModel.value }
])
const ccSwitchProjectUrl = 'https://github.com/farion1231/cc-switch'
const ccSwitchLatestReleaseUrl = `${ccSwitchProjectUrl}/releases/latest`
const ccSwitchTargets = computed(() => [
  { value: 'claude' as const, label: copy.value.guide.ccSwitch.targets.claude },
  { value: 'codex' as const, label: copy.value.guide.ccSwitch.targets.codex }
])
const ccSwitchDownloads = computed(() => copy.value.guide.osTabs.map((label) => ({
  label,
  href: ccSwitchLatestReleaseUrl
})))
const ccSwitchUsageScript = `({
  request: {
    url: "{{baseUrl}}/v1/usage",
    method: "GET",
    headers: { "Authorization": "Bearer {{apiKey}}" }
  },
  extractor: function(response) {
    const remaining = response?.remaining ?? response?.quota?.remaining ?? response?.balance;
    const unit = response?.unit ?? response?.quota?.unit ?? "USD";
    return {
      isValid: response?.is_active ?? response?.isValid ?? true,
      remaining,
      unit
    };
  }
})`
const ccSwitchImportDeeplink = computed(() => {
  const platform: GroupPlatform = selectedCcSwitchTarget.value === 'codex' ? 'openai' : 'anthropic'
  const clientType: CcSwitchClientType = 'claude'
  const baseUrl = window.location.origin.replace(/\/+$/, '')

  return buildCcSwitchImportDeeplink({
    baseUrl,
    platform,
    clientType,
    providerName: (siteDisplayName.value || 'sub2api').trim() || 'sub2api',
    apiKey: 'sk-your-key-here',
    usageScript: ccSwitchUsageScript
  })
})
const selectedGuideToolSlug = computed(() => selectedGuideTool.value.toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/^-|-$/g, ''))
const guideFileExtension = computed(() => {
  if (selectedGuideOs.value === 'Windows') return 'ps1'
  return isProfileSetupGuide.value ? 'sh' : 'env'
})
const guideFileName = computed(() => {
  const slug = siteDisplayName.value.toLowerCase().replace(/\s+/g, '-')
  const purpose = isProfileSetupGuide.value ? 'setup' : 'config.example'
  return `${slug}-${selectedGuideToolSlug.value}-${purpose}.${guideFileExtension.value}`
})
const guideRunCommand = computed(() => {
  if (selectedGuideOs.value === 'Windows') {
    return `powershell -ExecutionPolicy Bypass -File .\\${guideFileName.value}`
  }
  if (!isProfileSetupGuide.value) return `cat ~/Downloads/${guideFileName.value}`
  return `bash ~/Downloads/${guideFileName.value}`
})
const guideScript = computed(() => {
  const originBase = window.location.origin.replace(/\/+$/, '')
  const apiBase = isClaudeCodeGuide.value || isCodexGuide.value ? originBase : `${originBase}/api`
  const brandName = siteDisplayName.value || 'Titan Router'
  const blockName = `${brandName} Claude Code`
  const codexProviderID = 'sub2api'
  const codexBlockName = `${brandName} Codex`
  const osLabel = selectedGuideOs.value.toLowerCase()

  if (isHermesGuide.value || isOpenClawGuide.value) {
    return buildCompatibleProfileSetupScript({
      brandName,
      toolName: selectedGuideTool.value,
      os: selectedGuideOs.value,
      originBase,
      model: selectedGuideModel.value
    })
  }

  if (!isClaudeCodeGuide.value && !isCodexGuide.value) {
    if (selectedGuideOs.value === 'Windows') {
      return [
        `# ${brandName} config example for ${selectedGuideTool.value}`,
        '# Copy these values into your tool configuration.',
        `$env:TITANROUTER_BASE_URL="${apiBase}"`,
        '$env:TITANROUTER_API_KEY="sk-your-key-here"',
        `$env:TITANROUTER_MODEL="${selectedGuideModel.value}"`,
        '',
        `# Selected tool: ${selectedGuideTool.value}`
      ].join('\n')
    }

    const rcFile = selectedGuideOs.value === 'macOS' ? '$HOME/.zshrc' : '$HOME/.bashrc'
    return [
      '#!/usr/bin/env bash',
      `# ${brandName} config example for ${selectedGuideTool.value}`,
      '# Copy these values into your tool configuration.',
      '',
      `RC="${rcFile}"`,
      '# Shell export example:',
      '',
      `export TITANROUTER_BASE_URL="${apiBase}"`,
      'export TITANROUTER_API_KEY="sk-your-key-here"',
      `export TITANROUTER_MODEL="${selectedGuideModel.value}"`,
      '',
      `# Selected tool: ${selectedGuideTool.value}`,
      `# Suggested shell profile: ${rcFile}`
    ].join('\n')
  }

  if (isCodexGuide.value) {
    const codexModel = selectedGuideModel.value.startsWith('gpt-') ? selectedGuideModel.value : 'gpt-5.5'
    const codexConfigContent = [
      `# ${codexBlockName} start`,
      `model_provider = "${codexProviderID}"`,
      `model = "${codexModel}"`,
      `review_model = "${codexModel}"`,
      'model_reasoning_effort = "xhigh"',
      'disable_response_storage = true',
      'network_access = "enabled"',
      'windows_wsl_setup_acknowledged = true',
      '',
      `[model_providers.${codexProviderID}]`,
      `name = "${brandName}"`,
      `base_url = "${apiBase}"`,
      'wire_api = "responses"',
      'env_key = "OPENAI_API_KEY"',
      '',
      '[features]',
      'goals = true',
      `# ${codexBlockName} end`
    ].join('\n')

    if (selectedGuideOs.value === 'Windows') {
      return [
        `# ${brandName} one-click setup for Codex (${osLabel})`,
        '# Writes a Codex config and stores the API key in your PowerShell profile.',
        '$ErrorActionPreference = "Stop"',
        '',
        '$CodexDir = Join-Path $env:USERPROFILE ".codex"',
        '$ConfigPath = Join-Path $CodexDir "config.toml"',
        'if (!(Test-Path $CodexDir)) { New-Item -ItemType Directory -Path $CodexDir -Force | Out-Null }',
        'if (Test-Path $ConfigPath) {',
        '  Copy-Item $ConfigPath "$ConfigPath.bak.$([DateTimeOffset]::UtcNow.ToUnixTimeSeconds())"',
        '}',
        '',
        "Set-Content -Path $ConfigPath -Value @'",
        codexConfigContent,
        "'@",
        '',
        '$ProfilePath = $PROFILE',
        '$ProfileDir = Split-Path -Parent $ProfilePath',
        'if (!(Test-Path $ProfileDir)) { New-Item -ItemType Directory -Path $ProfileDir -Force | Out-Null }',
        'if (!(Test-Path $ProfilePath)) { New-Item -ItemType File -Path $ProfilePath -Force | Out-Null }',
        `$Start = "# ${codexBlockName} env start"`,
        `$End = "# ${codexBlockName} env end"`,
        '$ProfileContent = Get-Content -Raw -Path $ProfilePath',
        'if ($ProfileContent.Contains($Start)) {',
        '  $Pattern = "(?s)\\r?\\n?" + [regex]::Escape($Start) + ".*?" + [regex]::Escape($End) + "\\r?\\n?"',
        '  $ProfileContent = [regex]::Replace($ProfileContent, $Pattern, "`r`n").TrimEnd() + "`r`n"',
        '  Set-Content -Path $ProfilePath -Value $ProfileContent',
        '}',
        '',
        "Add-Content -Path $ProfilePath -Value @'",
        '',
        `# ${codexBlockName} env start`,
        '$env:OPENAI_API_KEY="sk-your-key-here"',
        `# ${codexBlockName} env end`,
        "'@",
        '',
        `Write-Host "[${brandName}] Codex config written to $ConfigPath"`,
        'if (!(Get-Command codex -ErrorAction SilentlyContinue)) {',
        '  Write-Host ""',
        `  Write-Host "[${brandName}] Codex CLI not installed yet. Install:"`,
        '  Write-Host "  npm install -g @openai/codex"',
        '  Write-Host ""',
        '  Write-Host "Then replace sk-your-key-here in your PowerShell profile, open a NEW PowerShell window, and run: codex"',
        '} else {',
        '  Write-Host ""',
        `  Write-Host "[${brandName}] Replace sk-your-key-here in your PowerShell profile, open a NEW PowerShell window, and run: codex"`,
        '}'
      ].join('\n')
    }

    const rcFile = selectedGuideOs.value === 'macOS' ? '$HOME/.zshrc' : '$HOME/.bashrc'
    return [
      '#!/usr/bin/env bash',
      `# ${brandName} one-click setup for Codex (${osLabel})`,
      '# Writes a Codex config and stores the API key in your shell profile.',
      'set -e',
      '',
      'CODEX_DIR="$HOME/.codex"',
      'CONFIG_PATH="$CODEX_DIR/config.toml"',
      `RC="${rcFile}"`,
      'mkdir -p "$CODEX_DIR"',
      'touch "$RC"',
      '',
      'if [ -f "$CONFIG_PATH" ]; then',
      '  cp "$CONFIG_PATH" "$CONFIG_PATH.bak.$(date +%s)"',
      'fi',
      '',
      'cat > "$CONFIG_PATH" <<\'EOF\'',
      codexConfigContent,
      'EOF',
      '',
      `ENV_START="# ${codexBlockName} env start"`,
      `ENV_END="# ${codexBlockName} env end"`,
      'if grep -q "$ENV_START" "$RC" 2>/dev/null; then',
      '  TMP="$RC.tmp.$$"',
      '  awk -v start="$ENV_START" -v end="$ENV_END" \'',
      '    $0 == start { skip = 1; next }',
      '    $0 == end { skip = 0; next }',
      '    !skip { print }',
      '  \' "$RC" > "$TMP" && mv "$TMP" "$RC"',
      'fi',
      '',
      'cat >> "$RC" <<\'EOF\'',
      '',
      `# ${codexBlockName} env start`,
      'export OPENAI_API_KEY="sk-your-key-here"',
      `# ${codexBlockName} env end`,
      'EOF',
      '',
      `echo "[${brandName}] Codex config written to $CONFIG_PATH"`,
      '. "$RC" 2>/dev/null || true',
      '',
      'if ! command -v codex >/dev/null 2>&1; then',
      '  echo ""',
      `  echo "[${brandName}] Codex CLI not installed yet. Install:"`,
      '  echo "  npm install -g @openai/codex"',
      '  echo ""',
      '  echo "Then replace sk-your-key-here in your shell profile, open a NEW terminal, and run: codex"',
      'else',
      '  echo ""',
      `  echo "[${brandName}] Replace sk-your-key-here in your shell profile, open a NEW terminal, and run: codex"`,
      'fi'
    ].join('\n')
  }

  if (selectedGuideOs.value === 'Windows') {
    return [
      `# ${brandName} one-click setup for Claude Code (${osLabel})`,
      "# Idempotent: re-running won't duplicate profile entries.",
      '$ErrorActionPreference = "Stop"',
      '',
      '$ProfilePath = $PROFILE',
      '$ProfileDir = Split-Path -Parent $ProfilePath',
      'if (!(Test-Path $ProfileDir)) { New-Item -ItemType Directory -Path $ProfileDir -Force | Out-Null }',
      'if (!(Test-Path $ProfilePath)) { New-Item -ItemType File -Path $ProfilePath -Force | Out-Null }',
      '',
      `$Start = "# ${blockName} start"`,
      `$End = "# ${blockName} end"`,
      '$Content = Get-Content -Raw -Path $ProfilePath',
      'if ($Content.Contains($Start) -or $Content.Contains("ANTHROPIC_AUTH_TOKEN=")) {',
      '  Copy-Item $ProfilePath "$ProfilePath.bak.$([DateTimeOffset]::UtcNow.ToUnixTimeSeconds())"',
      '  $Pattern = "(?s)\\r?\\n?" + [regex]::Escape($Start) + ".*?" + [regex]::Escape($End) + "\\r?\\n?"',
      '  $Content = [regex]::Replace($Content, $Pattern, "`r`n").TrimEnd() + "`r`n"',
      '  Set-Content -Path $ProfilePath -Value $Content',
      `  Write-Host "[${brandName}] Refreshed existing Claude Code block in $ProfilePath"`,
      '}',
      '',
      "Add-Content -Path $ProfilePath -Value @'",
      '',
      `# ${blockName} start`,
      `$env:ANTHROPIC_BASE_URL="${apiBase}"`,
      '$env:ANTHROPIC_AUTH_TOKEN="sk-your-key-here"',
      `$env:ANTHROPIC_MODEL="${selectedGuideModel.value}"`,
      '$env:CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC="1"',
      `# ${blockName} end`,
      "'@",
      '',
      `Write-Host "[${brandName}] Claude Code config written to $ProfilePath"`,
      'if (!(Get-Command claude -ErrorAction SilentlyContinue)) {',
      '  Write-Host ""',
      `  Write-Host "[${brandName}] Claude Code not installed yet. Install:"`,
      '  Write-Host "  npm install -g @anthropic-ai/claude-code"',
      '  Write-Host ""',
      '  Write-Host "Then re-open PowerShell and run: claude"',
      '} else {',
      '  Write-Host ""',
      `  Write-Host "[${brandName}] All set. Open a NEW PowerShell window and run: claude"`,
      '}'
    ].join('\n')
  }

  const rcFile = selectedGuideOs.value === 'macOS' ? '$HOME/.zshrc' : '$HOME/.bashrc'
  return [
    '#!/usr/bin/env bash',
    `# ${brandName} one-click setup for Claude Code (${osLabel})`,
    "# Idempotent: re-running won't duplicate exports.",
    'set -e',
    '',
    `RC="${rcFile}"`,
    `BLOCK_START="# ${blockName} start"`,
    `BLOCK_END="# ${blockName} end"`,
    'touch "$RC"',
    '',
    'if grep -q "$BLOCK_START" "$RC" 2>/dev/null || grep -q "ANTHROPIC_AUTH_TOKEN=" "$RC" 2>/dev/null; then',
    '  cp "$RC" "$RC.bak.$(date +%s)"',
    '  TMP="$RC.tmp.$$"',
    '  awk -v start="$BLOCK_START" -v end="$BLOCK_END" \'',
    '    $0 == start { skip = 1; next }',
    '    $0 == end { skip = 0; next }',
    '    !skip { print }',
    '  \' "$RC" > "$TMP" && mv "$TMP" "$RC"',
    `  echo "[${brandName}] Refreshed existing Claude Code block in $RC"`,
    'fi',
    '',
    'cat >> "$RC" <<\'EOF\'',
    '',
    `# ${blockName} start`,
    `export ANTHROPIC_BASE_URL="${apiBase}"`,
    'export ANTHROPIC_AUTH_TOKEN="sk-your-key-here"',
    `export ANTHROPIC_MODEL="${selectedGuideModel.value}"`,
    'export CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1',
    `# ${blockName} end`,
    'EOF',
    '',
    `echo "[${brandName}] Claude Code config written to $RC"`,
    '. "$RC" 2>/dev/null || true',
    '',
    'if ! command -v claude >/dev/null 2>&1; then',
    '  echo ""',
    `  echo "[${brandName}] Claude Code not installed yet. Install:"`,
    '  echo "  npm install -g @anthropic-ai/claude-code"',
    '  echo ""',
    '  echo "Then re-open your terminal and run: claude"',
    'else',
    '  echo ""',
    `  echo "[${brandName}] All set. Open a NEW terminal and run: claude"`,
    'fi'
  ].join('\n')
})
const displayPlans = computed<DisplayPlan[]>(() => {
  if (publicPlans.value.length > 0) {
    return publicPlans.value.map((plan) => toDisplayPlan(plan))
  }

  return copy.value.plans.map((plan, index) => ({
    id: `fallback-${index}`,
    name: plan.name,
    desc: plan.desc,
    badge: plan.badge,
    price: plan.price,
    originalPrice: '',
    suffix: '',
    features: [],
    meta: []
  }))
})

const homeCopies: Record<'zh' | 'en', HomeCopy> = {
  zh: {
    navItems: [
      { label: '文档', href: '#docs' },
      { label: '定价', href: '#pricing' },
      { label: '用户评价', href: '#reviews' },
      { label: '常见问题', href: '#faq' },
      { label: '联系我们', href: '#contact' }
    ],
    auth: { login: '登录', register: '注册', dashboard: '控制台' },
    eyebrow: '面向生产团队的 AI API 路由',
    heroTitle: '重构您的',
    heroTitleSuffix: 'AI 编程体验',
    heroSubtitle: '我们把 AI 编程、使用管理与团队协作整合到一个软件平台，帮助你专注产品本身，而不是在多个工具之间来回切换。',
    cta: { primary: '立即体验', secondary: '查看定价', guide: '一键接入', start: '开始使用', subscribe: '立即开通', loginToSubscribe: '登录后开通' },
    heroFeatures: [
      { icon: 'shield', title: '高效工作台', desc: '统一管理 AI 编码任务、配额与使用体验。' },
      { icon: 'bolt', title: '稳定体验', desc: '流畅响应、可靠交付，适合日常开发协作。' },
      { icon: 'terminal', title: '专业服务', desc: '1 对 1 工程师支持与方案建议。' }
    ],
    metricsIntro: '为高频 AI 应用提供稳定接入层',
    metrics: [
      { value: '10,000+', label: '开发者用户' },
      { value: '99.9%', label: '服务稳定性' },
      { value: '500万+', label: '请求处理次数' },
      { value: '1v1', label: '专属支持' }
    ],
    guide: {
      eyebrow: '一键接入',
      badge: '进群领试用额度',
      title: '选个工具，下载脚本，双击运行 —— 不用打开终端。',
      close: '关闭',
      tools: [
        { name: 'CC Switch', icon: '🎛️' },
        { name: 'Claude Code', icon: '🧠' },
        { name: 'Codex', icon: '💻' },
        { name: 'Cursor', icon: '📝' },
        { name: 'Hermes', icon: '⚡' },
        { name: 'OpenClaw', icon: '🦞' }
      ],
      modelLabel: '模型',
      models: ['claude-opus-4-7', 'gpt-5.5', 'gpt-5', 'gemini-2.5-pro', 'claude-sonnet-4'],
      modelHint: '不在列表里？后续可在工具配置里手动填写',
      osTabs: ['macOS', 'Linux', 'Windows'],
      detected: '当前选择',
      instruction: '下面是当前工具的接入配置。下载后按提示执行，或复制配置到对应工具。',
      copy: '复制配置',
      copied: '已复制',
      download: '下载配置脚本',
      or: '或',
      tip: '推荐：先在网页里试一下，确认账号和额度可用，再按工具文档接入本地工具。',
      keyNotice: 'sk-your-key-here 是占位符。登录并创建 Key 后再替换，否则网关会拒绝请求。',
      ccSwitch: {
        title: 'CC Switch 一键导入',
        description: 'GUI 工具，一键导入当前站点到 Claude Code / Codex，免改 env / config.toml。',
        importLabel: '导入到',
        targets: { claude: 'Claude Code', codex: 'Codex' },
        subModel: '指定副模型（Haiku / Sonnet / Opus）可在 CC Switch 内继续调整',
        open: '打开 CC Switch',
        notInstalled: '没装先下载',
        project: '项目主页',
        notice: '首页展示的是占位 Key。登录后到 Key 管理页可以把真实 Key 一键导入 CC Switch。'
      },
      cursor: {
        description: 'Cursor 需要手动配置，把下面三项复制到 Settings -> Models 即可。',
        title: 'Cursor 需要手动配置（约 1 分钟）',
        steps: [
          '打开 Cursor 右上角齿轮，进入 Settings -> Models。',
          '勾选 Override OpenAI Base URL。',
          '把下面的 Base URL、API Key 和 Model 填进去。',
          '在顶部模型选择里选择你要用的模型。'
        ],
        notice: '登录后这里会自动填入你的真实 Key；当前 sk-your-key-here 只是占位符。'
      }
    },
    docs: {
      eyebrow: 'Unified gateway',
      title: '一个入口，连接多种 AI 能力',
      desc: '统一 OpenAI 兼容接口、订阅账户池和团队 API Key，把复杂调度留在网关层。'
    },
    capabilityCards: [
      { icon: 'key', title: 'API Key 分发', desc: '为成员、项目和客户创建独立密钥，便于权限隔离。' },
      { icon: 'sync', title: '账户池调度', desc: '自动在多账号之间分配请求，支持粘性会话策略。' },
      { icon: 'chart', title: '用量统计', desc: '按用户、模型、密钥追踪调用量和成本变化。' },
      { icon: 'creditCard', title: '余额计费', desc: '内置充值、兑换码和套餐能力，适合商业化运营。' }
    ],
    pricing: { eyebrow: 'Pricing', title: '从个人开发到团队协作' },
    plans: [
      { name: 'Starter', price: '¥29/月', desc: '适合个人开发者和小型自动化任务。', badge: '' },
      { name: 'Pro', price: '¥99/月', desc: '适合持续开发、团队协作和更高并发。', badge: '推荐' },
      { name: 'Business', price: '联系销售', desc: '适合需要定制额度、专属支持和部署方案的团队。', badge: '' }
    ],
    planLabels: { days: '天', rate: '倍率', daily: '日限额', weekly: '周限额', monthly: '月限额', quota: '额度', unlimited: '不限' },
    reviews: {
      eyebrow: 'Reviews',
      items: [
        { name: '独立开发者', quote: '统一 Key 之后，多个 AI 工具的接入和成本核算清楚很多。' },
        { name: '产品团队负责人', quote: '稳定性和后台可观测性是我们选择 titanrouter 的主要原因。' },
        { name: '企业技术顾问', quote: '对团队来说，账户池、计费和权限管理比单纯转发更重要。' }
      ]
    },
    faq: {
      titlePrefix: '有疑问？我们来',
      titleAccent: '解答',
      items: [
        { question: '什么是 titanrouter？', answer: 'titanrouter 是面向开发者和团队的 AI API 路由平台，提供统一接口、密钥分发、账户调度、订阅套餐与用量管理等能力，帮助团队更高效地使用 AI 服务。' },
        { question: '为什么选择 titanrouter？', answer: '我们把常用 AI 开发能力、计费管理和客户支持整合在同一个平台里，减少工具切换成本，适合个人开发者和团队持续使用。' },
        { question: '服务稳定性如何保障？', answer: '平台通过账户池调度、可用性监控、失败重试和用量限制等机制提升稳定性，核心能力面向高频开发场景持续维护。' },
        { question: '如何开始使用服务？', answer: '注册账号后选择合适的订阅套餐，即可进入控制台创建 API Key。平台提供清晰的接入说明、购买入口和基础支持。' }
      ]
    },
    footer: { docs: '文档' }
  },
  en: {
    navItems: [
      { label: 'Docs', href: '#docs' },
      { label: 'Pricing', href: '#pricing' },
      { label: 'Reviews', href: '#reviews' },
      { label: 'FAQ', href: '#faq' },
      { label: 'Contact', href: '#contact' }
    ],
    auth: { login: 'Login', register: 'Sign up', dashboard: 'Dashboard' },
    eyebrow: 'AI API routing built for production teams',
    heroTitle: 'Rebuild your',
    heroTitleSuffix: 'AI coding experience',
    heroSubtitle: 'We bring AI coding, usage management, and team collaboration into one software platform so you can focus on the product instead of switching between tools.',
    cta: { primary: 'Get started', secondary: 'View pricing', guide: 'One-click setup', start: 'Start now', subscribe: 'Subscribe now', loginToSubscribe: 'Login to subscribe' },
    heroFeatures: [
      { icon: 'shield', title: 'Efficient workspace', desc: 'Manage AI coding tasks, quotas, and usage experience in one place.' },
      { icon: 'bolt', title: 'Stable experience', desc: 'Smooth response and reliable delivery for daily development workflows.' },
      { icon: 'terminal', title: 'Expert service', desc: '1-on-1 engineering support and solution guidance.' }
    ],
    metricsIntro: 'A stable access layer for high-frequency AI applications',
    metrics: [
      { value: '10,000+', label: 'Developers' },
      { value: '99.9%', label: 'Service uptime' },
      { value: '5M+', label: 'Requests handled' },
      { value: '1v1', label: 'Dedicated support' }
    ],
    guide: {
      eyebrow: 'One-click setup',
      badge: 'Join the group for trial credits',
      title: 'Choose a tool, download the setup, and run it without opening a terminal.',
      close: 'Close',
      tools: [
        { name: 'CC Switch', icon: '🎛️' },
        { name: 'Claude Code', icon: '🧠' },
        { name: 'Codex', icon: '💻' },
        { name: 'Cursor', icon: '📝' },
        { name: 'Hermes', icon: '⚡' },
        { name: 'OpenClaw', icon: '🦞' }
      ],
      modelLabel: 'Model',
      models: ['claude-opus-4-7', 'gpt-5.5', 'gpt-5', 'gemini-2.5-pro', 'claude-sonnet-4'],
      modelHint: 'Not listed? Fill it in manually in your tool config later.',
      osTabs: ['macOS', 'Linux', 'Windows'],
      detected: 'Selected',
      instruction: 'Use the setup below for the selected tool. Download and run it, or copy the config into the tool.',
      copy: 'Copy config',
      copied: 'Copied',
      download: 'Download setup script',
      or: 'or',
      tip: 'Recommended: try it in the web app first, then connect your local tool with the tool-specific guide.',
      keyNotice: 'sk-your-key-here is a placeholder. Login, create a Key, and replace it before using the gateway.',
      ccSwitch: {
        title: 'CC Switch one-click import',
        description: 'A GUI tool that imports this site into Claude Code / Codex without editing env files or config.toml.',
        importLabel: 'Import to',
        targets: { claude: 'Claude Code', codex: 'Codex' },
        subModel: 'Sub-models such as Haiku / Sonnet / Opus can be adjusted in CC Switch.',
        open: 'Open CC Switch',
        notInstalled: 'Download first',
        project: 'Project',
        notice: 'This homepage shows a placeholder Key. After login, use the Key management page to import a real Key into CC Switch.'
      },
      cursor: {
        description: 'Cursor requires manual setup. Copy the three values below into Settings -> Models.',
        title: 'Cursor requires manual setup (about 1 minute)',
        steps: [
          'Open the gear icon in Cursor, then go to Settings -> Models.',
          'Enable Override OpenAI Base URL.',
          'Paste the Base URL, API Key, and Model below.',
          'Select the model you want to use from the model picker.'
        ],
        notice: 'After login, this can be filled with your real Key. sk-your-key-here is only a placeholder.'
      }
    },
    docs: {
      eyebrow: 'Unified gateway',
      title: 'One entry point for multiple AI capabilities',
      desc: 'Unify OpenAI-compatible APIs, subscription account pools, and team API keys while keeping complex scheduling inside the gateway.'
    },
    capabilityCards: [
      { icon: 'key', title: 'API key distribution', desc: 'Create separate keys for members, projects, and customers with clear isolation.' },
      { icon: 'sync', title: 'Account pool scheduling', desc: 'Distribute requests across multiple accounts with sticky-session support.' },
      { icon: 'chart', title: 'Usage analytics', desc: 'Track usage and cost by user, model, and key.' },
      { icon: 'creditCard', title: 'Balance billing', desc: 'Built-in top-ups, promo codes, and subscription plans for commercial operation.' }
    ],
    pricing: { eyebrow: 'Pricing', title: 'From solo development to team workflows' },
    plans: [
      { name: 'Starter', price: '$4/mo', desc: 'For individual developers and small automation tasks.', badge: '' },
      { name: 'Pro', price: '$14/mo', desc: 'For continuous development, team usage, and higher concurrency.', badge: 'Popular' },
      { name: 'Business', price: 'Contact us', desc: 'For custom quota, dedicated support, and deployment guidance.', badge: '' }
    ],
    planLabels: { days: 'days', rate: 'Rate', daily: 'Daily', weekly: 'Weekly', monthly: 'Monthly', quota: 'Quota', unlimited: 'Unlimited' },
    reviews: {
      eyebrow: 'Reviews',
      items: [
        { name: 'Independent developer', quote: 'After unifying keys, tool access and cost tracking became much clearer.' },
        { name: 'Product lead', quote: 'Reliability and backend observability are why we chose titanrouter.' },
        { name: 'Enterprise consultant', quote: 'For teams, account pools, billing, and permission control matter more than simple forwarding.' }
      ]
    },
    faq: {
      titlePrefix: 'Questions? We have ',
      titleAccent: 'answers',
      items: [
        { question: 'What is titanrouter?', answer: 'titanrouter is an AI API routing platform for developers and teams. It provides unified endpoints, key distribution, account scheduling, subscription plans, and usage management.' },
        { question: 'Why choose titanrouter?', answer: 'It brings AI development access, billing management, and customer support into one platform, reducing tool switching for individuals and teams.' },
        { question: 'How is service stability handled?', answer: 'The platform improves stability through account pool scheduling, availability monitoring, retry behavior, and quota controls for frequent development workloads.' },
        { question: 'How do I get started?', answer: 'Create an account, choose a subscription plan, and generate an API Key in the dashboard. The platform provides clear onboarding, purchase entry points, and basic support.' }
      ]
    },
    footer: { docs: 'Docs' }
  }
}

function openGuideModal() {
  showGuideModal.value = true
  copiedGuideScript.value = false
}

function selectGuideTool(toolName: string) {
  selectedGuideTool.value = toolName
  copiedGuideScript.value = false
  copiedCursorField.value = ''
  if (toolName === 'CC Switch') {
    selectedCcSwitchTarget.value = 'claude'
  }
  if (toolName === 'Codex' && !selectedGuideModel.value.startsWith('gpt-')) {
    selectedGuideModel.value = 'gpt-5.5'
  }
  if (toolName === 'Claude Code' && !selectedGuideModel.value.startsWith('claude-')) {
    selectedGuideModel.value = 'claude-opus-4-7'
  }
}

function openCcSwitchImport() {
  window.location.href = ccSwitchImportDeeplink.value
}

function resolveCompatibleProvider(model: string): 'anthropic' | 'openai' | 'gemini' {
  if (model.startsWith('gpt-')) return 'openai'
  if (model.startsWith('gemini-')) return 'gemini'
  return 'anthropic'
}

function buildCompatibleProfileSetupScript(input: {
  brandName: string
  toolName: string
  os: string
  originBase: string
  model: string
}): string {
  const blockName = `${input.brandName} ${input.toolName}`
  const osLabel = input.os.toLowerCase()
  const prefix = input.toolName.toUpperCase().replace(/[^A-Z0-9]+/g, '_')
  const provider = resolveCompatibleProvider(input.model)
  const openAIBase = `${input.originBase}/v1`
  const anthropicBase = input.originBase
  const geminiBase = input.originBase

  const shellExports = [
    `export ${prefix}_PROVIDER="${provider}"`,
    `export ${prefix}_BASE_URL="${openAIBase}"`,
    `export ${prefix}_API_KEY="sk-your-key-here"`,
    `export ${prefix}_MODEL="${input.model}"`,
    '',
    `export OPENAI_BASE_URL="${openAIBase}"`,
    'export OPENAI_API_KEY="sk-your-key-here"',
    `export OPENAI_MODEL="${input.model}"`,
    '',
    `export ANTHROPIC_BASE_URL="${anthropicBase}"`,
    'export ANTHROPIC_AUTH_TOKEN="sk-your-key-here"',
    'export ANTHROPIC_API_KEY="sk-your-key-here"',
    `export ANTHROPIC_MODEL="${input.model}"`,
    '',
    `export GEMINI_BASE_URL="${geminiBase}"`,
    `export GOOGLE_GEMINI_BASE_URL="${geminiBase}"`,
    'export GEMINI_API_KEY="sk-your-key-here"',
    'export GOOGLE_API_KEY="sk-your-key-here"',
    `export GEMINI_MODEL="${input.model}"`
  ]

  if (input.os === 'Windows') {
    const profileAssignments = [
      `$env:${prefix}_PROVIDER="${provider}"`,
      `$env:${prefix}_BASE_URL="${openAIBase}"`,
      `$env:${prefix}_API_KEY="sk-your-key-here"`,
      `$env:${prefix}_MODEL="${input.model}"`,
      '',
      `$env:OPENAI_BASE_URL="${openAIBase}"`,
      '$env:OPENAI_API_KEY="sk-your-key-here"',
      `$env:OPENAI_MODEL="${input.model}"`,
      '',
      `$env:ANTHROPIC_BASE_URL="${anthropicBase}"`,
      '$env:ANTHROPIC_AUTH_TOKEN="sk-your-key-here"',
      '$env:ANTHROPIC_API_KEY="sk-your-key-here"',
      `$env:ANTHROPIC_MODEL="${input.model}"`,
      '',
      `$env:GEMINI_BASE_URL="${geminiBase}"`,
      `$env:GOOGLE_GEMINI_BASE_URL="${geminiBase}"`,
      '$env:GEMINI_API_KEY="sk-your-key-here"',
      '$env:GOOGLE_API_KEY="sk-your-key-here"',
      `$env:GEMINI_MODEL="${input.model}"`
    ]

    return [
      `# ${input.brandName} one-click setup for ${input.toolName} (${osLabel})`,
      '# Writes common OpenAI / Anthropic / Gemini-compatible variables to your PowerShell profile.',
      '$ErrorActionPreference = "Stop"',
      '',
      '$ProfilePath = $PROFILE',
      '$ProfileDir = Split-Path -Parent $ProfilePath',
      'if (!(Test-Path $ProfileDir)) { New-Item -ItemType Directory -Path $ProfileDir -Force | Out-Null }',
      'if (!(Test-Path $ProfilePath)) { New-Item -ItemType File -Path $ProfilePath -Force | Out-Null }',
      '',
      `$Start = "# ${blockName} start"`,
      `$End = "# ${blockName} end"`,
      '$Content = Get-Content -Raw -Path $ProfilePath',
      'if ($Content.Contains($Start)) {',
      '  Copy-Item $ProfilePath "$ProfilePath.bak.$([DateTimeOffset]::UtcNow.ToUnixTimeSeconds())"',
      '  $Pattern = "(?s)\\r?\\n?" + [regex]::Escape($Start) + ".*?" + [regex]::Escape($End) + "\\r?\\n?"',
      '  $Content = [regex]::Replace($Content, $Pattern, "`r`n").TrimEnd() + "`r`n"',
      '  Set-Content -Path $ProfilePath -Value $Content',
      `  Write-Host "[${input.brandName}] Refreshed existing ${input.toolName} block in $ProfilePath"`,
      '}',
      '',
      "Add-Content -Path $ProfilePath -Value @'",
      '',
      `# ${blockName} start`,
      ...profileAssignments,
      `# ${blockName} end`,
      "'@",
      '',
      `Write-Host "[${input.brandName}] ${input.toolName} config written to $ProfilePath"`,
      'Write-Host "Replace sk-your-key-here with your real Key, then open a NEW PowerShell window."'
    ].join('\n')
  }

  const rcFile = input.os === 'macOS' ? '$HOME/.zshrc' : '$HOME/.bashrc'
  return [
    '#!/usr/bin/env bash',
    `# ${input.brandName} one-click setup for ${input.toolName} (${osLabel})`,
    '# Writes common OpenAI / Anthropic / Gemini-compatible variables to your shell profile.',
    'set -e',
    '',
    `RC="${rcFile}"`,
    `BLOCK_START="# ${blockName} start"`,
    `BLOCK_END="# ${blockName} end"`,
    'touch "$RC"',
    '',
    'if grep -q "$BLOCK_START" "$RC" 2>/dev/null; then',
    '  cp "$RC" "$RC.bak.$(date +%s)"',
    '  TMP="$RC.tmp.$$"',
    '  awk -v start="$BLOCK_START" -v end="$BLOCK_END" \'',
    '    $0 == start { skip = 1; next }',
    '    $0 == end { skip = 0; next }',
    '    !skip { print }',
    '  \' "$RC" > "$TMP" && mv "$TMP" "$RC"',
    `  echo "[${input.brandName}] Refreshed existing ${input.toolName} block in $RC"`,
    'fi',
    '',
    'cat >> "$RC" <<\'EOF\'',
    '',
    `# ${blockName} start`,
    ...shellExports,
    `# ${blockName} end`,
    'EOF',
    '',
    `echo "[${input.brandName}] ${input.toolName} config written to $RC"`,
    '. "$RC" 2>/dev/null || true',
    'echo "Replace sk-your-key-here with your real Key, then open a NEW terminal."'
  ].join('\n')
}

async function copyCursorConfigValue(fieldKey: string, value: string) {
  try {
    await navigator.clipboard.writeText(value)
    copiedCursorField.value = fieldKey
    window.setTimeout(() => {
      if (copiedCursorField.value === fieldKey) copiedCursorField.value = ''
    }, 1800)
  } catch (error) {
    console.warn('[home] Failed to copy cursor config:', error)
  }
}

function closeGuideModal() {
  showGuideModal.value = false
}

async function copyGuideScript() {
  try {
    await navigator.clipboard.writeText(guideScript.value)
    copiedGuideScript.value = true
    window.setTimeout(() => {
      copiedGuideScript.value = false
    }, 1800)
  } catch (error) {
    console.warn('[home] Failed to copy guide script:', error)
  }
}

function downloadGuideScript() {
  const blob = new Blob([guideScript.value], { type: 'text/plain;charset=utf-8' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = guideFileName.value
  document.body.appendChild(link)
  link.click()
  link.remove()
  URL.revokeObjectURL(url)
}

function parsePlanFeatures(features: SubscriptionPlan['features'] | string): string[] {
  if (Array.isArray(features)) return features.filter(Boolean)
  if (!features) return []

  try {
    const parsed = JSON.parse(features)
    if (Array.isArray(parsed)) {
      return parsed.map(String).filter(Boolean)
    }
  } catch {
    // Stored plan features may also be newline-separated text.
  }

  return features.split('\n').map((item) => item.trim()).filter(Boolean)
}

function formatCurrency(value: number): string {
  return new Intl.NumberFormat(locale.value === 'zh' ? 'zh-CN' : 'en-US', {
    style: 'currency',
    currency: 'CNY',
    maximumFractionDigits: value % 1 === 0 ? 0 : 2
  }).format(value)
}

function formatValidity(plan: SubscriptionPlan): string {
  const unit = plan.validity_unit || 'day'
  if (unit === 'day') return `/ ${plan.validity_days} ${copy.value.planLabels.days}`
  return `/ ${plan.validity_days} ${unit}`
}

function formatLimit(value: number | null | undefined): string {
  if (value == null) return copy.value.planLabels.unlimited
  return `$${value}`
}

function toDisplayPlan(plan: SubscriptionPlan): DisplayPlan {
  const meta = [
    { label: copy.value.planLabels.rate, value: `×${plan.rate_multiplier ?? 1}` },
    { label: copy.value.planLabels.daily, value: formatLimit(plan.daily_limit_usd) },
    { label: copy.value.planLabels.weekly, value: formatLimit(plan.weekly_limit_usd) },
    { label: copy.value.planLabels.monthly, value: formatLimit(plan.monthly_limit_usd) }
  ]

  const discount = plan.original_price && plan.original_price > plan.price
    ? `${Math.round((1 - plan.price / plan.original_price) * 100)}% OFF`
    : ''

  return {
    id: String(plan.id),
    name: plan.name,
    desc: plan.description || plan.group_name || '',
    badge: discount || plan.group_platform || '',
    price: formatCurrency(plan.price),
    originalPrice: plan.original_price ? formatCurrency(plan.original_price) : '',
    suffix: formatValidity(plan),
    features: parsePlanFeatures(plan.features).slice(0, 4),
    meta
  }
}

async function fetchPublicPlans() {
  try {
    const response = await apiClient.get<SubscriptionPlan[]>('/payment/public/plans')
    publicPlans.value = (response.data || [])
      .map((plan: Omit<SubscriptionPlan, 'features'> & { features: string | string[] }) => ({
        ...plan,
        features: parsePlanFeatures(plan.features)
      }))
      .sort((a, b) => (a.sort_order ?? 0) - (b.sort_order ?? 0))
  } catch (error) {
    console.warn('[home] Failed to fetch public subscription plans:', error)
  } finally {
    publicPlansLoaded.value = true
  }
}

watch(locale, () => {
  document.title = locale.value === 'zh' ? '首页 - Titan Router' : 'Home - Titan Router'
})

watch(showGuideModal, (isOpen) => {
  document.body.style.overflow = isOpen ? 'hidden' : ''
})

onMounted(() => {
  document.title = locale.value === 'zh' ? '首页 - Titan Router' : 'Home - Titan Router'

  authStore.checkAuth()
  if (!appStore.publicSettingsLoaded) {
    appStore.fetchPublicSettings()
  }
  fetchPublicPlans()
})

onUnmounted(() => {
  document.body.style.overflow = ''
})
</script>

<style scoped>
.home-shell {
  color-scheme: dark;
}

.home-hero {
  background:
    radial-gradient(circle at 50% 22%, rgba(56, 163, 250, 0.12), transparent 24rem),
    radial-gradient(circle at 86% 28%, rgba(56, 163, 250, 0.08), transparent 24rem),
    radial-gradient(circle at 12% 88%, rgba(251, 146, 60, 0.08), transparent 22rem),
    linear-gradient(180deg, #080d15 0%, #03060b 58%, #010204 100%);
}

.home-grid {
  background-image:
    linear-gradient(rgba(56, 163, 250, 0.022) 1px, transparent 1px),
    linear-gradient(90deg, rgba(56, 163, 250, 0.018) 1px, transparent 1px);
  background-size: 72px 72px;
  mask-image: radial-gradient(ellipse at center, black 0%, transparent 72%);
}

.home-beam {
  background:
    linear-gradient(112deg, transparent 0%, transparent 44%, rgba(56, 163, 250, 0.05) 50%, transparent 58%, transparent 100%),
    linear-gradient(180deg, rgba(255, 255, 255, 0.06), transparent 14%);
}

.brand-gradient {
  background: linear-gradient(90deg, #9bd2ff 0%, #38a3fa 48%, #168cec 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.guide-modal select option {
  background: #07101b;
  color: #fff;
}

.section-dark {
  background:
    radial-gradient(circle at 20% 0%, rgba(56, 163, 250, 0.12), transparent 24rem),
    #03060b;
}

.section-darker {
  background:
    radial-gradient(circle at 80% 0%, rgba(56, 163, 250, 0.1), transparent 24rem),
    #020409;
}

@media (max-width: 640px) {
  .hero-feature-card {
    min-height: 6rem;
  }
}
</style>
