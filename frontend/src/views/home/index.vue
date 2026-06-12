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
        <section class="guide-modal max-h-[92vh] w-full max-w-4xl overflow-y-auto rounded-2xl border border-[#38a3fa]/24 bg-black p-5 text-white shadow-[0_30px_120px_rgba(0,0,0,0.72)] sm:p-7">
          <div class="flex items-start justify-between gap-4">
            <div>
              <div class="flex flex-wrap items-center gap-2 text-sm font-semibold text-[#38a3fa]">
                <span>{{ copy.guide.eyebrow }}</span>
                <span class="rounded-full border border-[#38a3fa]/22 bg-[#38a3fa]/10 px-3 py-1 text-xs text-[#9bd2ff]">
                  {{ copy.guide.badge }}
                </span>
              </div>
              <h2 class="mt-2 text-xl font-black text-white sm:text-2xl">{{ copy.guide.title }}</h2>
            </div>
            <button
              type="button"
              class="rounded-lg p-2 text-slate-400 transition hover:bg-white/10 hover:text-white"
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
                : 'border-white/16 bg-[#101010] text-slate-200 hover:border-[#38a3fa]/45 hover:bg-[#151515]'"
              @click="selectedGuideTool = tool.name"
            >
              <span>{{ tool.icon }}</span>
              {{ tool.name }}
            </button>
          </div>

          <div class="mt-5 flex flex-col gap-3 text-sm sm:flex-row sm:items-center">
            <label class="text-slate-400" for="guide-model">{{ copy.guide.modelLabel }}</label>
            <select
              id="guide-model"
              v-model="selectedGuideModel"
              class="h-10 rounded-lg border border-white/16 bg-[#101010] px-3 font-medium text-white outline-none transition focus:border-[#38a3fa] focus:ring-2 focus:ring-[#38a3fa]/18"
            >
              <option v-for="model in copy.guide.models" :key="model" :value="model">{{ model }}</option>
            </select>
            <span class="text-slate-500">{{ copy.guide.modelHint }}</span>
          </div>

          <div class="mt-4 flex flex-wrap items-center justify-between gap-3">
            <div class="inline-flex rounded-lg border border-white/16 bg-[#101010] p-1">
              <button
                v-for="os in copy.guide.osTabs"
                :key="os"
                type="button"
                class="h-9 rounded-md px-4 text-sm font-semibold transition"
                :class="selectedGuideOs === os ? 'bg-[#38a3fa] text-white' : 'text-slate-400 hover:bg-white/8 hover:text-white'"
                @click="selectedGuideOs = os"
              >
                {{ os }}
              </button>
            </div>
            <span class="text-sm text-slate-500">{{ copy.guide.detected }} {{ selectedGuideOs }}</span>
          </div>

          <p class="mt-4 text-sm leading-6 text-slate-400">{{ copy.guide.instruction }}</p>

          <div class="relative mt-3 overflow-hidden rounded-lg border border-white/18 bg-[#050505]">
            <pre class="max-h-48 overflow-auto p-4 pr-14 text-left text-xs leading-5 text-slate-100"><code>{{ guideScript }}</code></pre>
            <button
              type="button"
              class="absolute right-3 top-3 rounded-md border border-white/14 bg-[#141414] p-2 text-slate-300 shadow-sm transition hover:bg-[#202020] hover:text-white"
              :title="copiedGuideScript ? copy.guide.copied : copy.guide.copy"
              @click="copyGuideScript"
            >
              <Icon name="copy" size="sm" />
            </button>
          </div>

          <div class="mt-4 flex flex-col gap-3 sm:flex-row sm:items-center">
            <button
              type="button"
              class="inline-flex h-10 items-center justify-center gap-2 rounded-lg border border-white/16 bg-[#101010] px-4 text-sm font-semibold text-slate-200 transition hover:border-[#38a3fa]/45 hover:bg-[#151515] hover:text-white"
              @click="downloadGuideScript"
            >
              <Icon name="download" size="sm" />
              {{ copy.guide.download }}
            </button>
            <span class="text-sm text-slate-500">{{ copy.guide.or }}</span>
            <code class="rounded-md border border-white/16 bg-[#101010] px-3 py-2 text-xs text-slate-200">
              {{ guideRunCommand }}
            </code>
          </div>

          <p class="mt-4 text-sm leading-6 text-slate-500">{{ copy.guide.tip }}</p>

          <div class="mt-4 rounded-lg border border-[#38a3fa]/28 bg-[#06111d] px-4 py-3 text-sm leading-6 text-[#d5ebff]">
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
const selectedGuideTool = ref('Claude Code')
const selectedGuideOs = ref('macOS')
const selectedGuideModel = ref('claude-opus-4-7')
const copiedGuideScript = ref(false)

const isAuthenticated = computed(() => authStore.isAuthenticated)
const dashboardPath = computed(() => (authStore.isAdmin ? '/admin/dashboard' : '/dashboard'))
const docUrl = computed(() => appStore.cachedPublicSettings?.doc_url || appStore.docUrl || '')
const siteDisplayName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'titanrouter')
const currentYear = computed(() => new Date().getFullYear())
const copy = computed<HomeCopy>(() => homeCopies[locale.value === 'zh' ? 'zh' : 'en'])
const guideRunCommand = computed(() => {
  const filename = `${siteDisplayName.value.toLowerCase().replace(/\s+/g, '-')}-config.example`
  if (selectedGuideOs.value === 'Windows') return `powershell -ExecutionPolicy Bypass -File .\\${filename}`
  return `cat ~/Downloads/${filename}`
})
const guideScript = computed(() => {
  const apiBase = `${window.location.origin}/api`
  if (selectedGuideOs.value === 'Windows') {
    return [
      '# titanrouter config example',
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
    '# titanrouter config example',
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
      title: '选个工具，复制配置，按说明接入。',
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
      models: ['claude-opus-4-7', 'gpt-5', 'gemini-2.5-pro', 'claude-sonnet-4'],
      modelHint: '不在列表里？后续可在工具配置里手动填写',
      osTabs: ['macOS', 'Linux', 'Windows'],
      detected: '当前选择',
      instruction: '下面是通用环境变量配置示例。不同工具的真实接入方式可能不同，后续会按工具适配。',
      copy: '复制配置',
      copied: '已复制',
      download: '下载配置示例',
      or: '或',
      tip: '推荐：先在网页里试一下，确认账号和额度可用，再按工具文档接入本地工具。',
      keyNotice: 'sk-your-key-here 是占位符。登录并创建 Key 后再替换，否则网关会拒绝请求。'
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
      title: 'Choose a tool, copy the config, and follow the setup notes.',
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
      models: ['claude-opus-4-7', 'gpt-5', 'gemini-2.5-pro', 'claude-sonnet-4'],
      modelHint: 'Not listed? Fill it in manually in your tool config later.',
      osTabs: ['macOS', 'Linux', 'Windows'],
      detected: 'Selected',
      instruction: 'This is a generic environment variable example. Real setup differs by tool and will be adapted next.',
      copy: 'Copy config',
      copied: 'Copied',
      download: 'Download config example',
      or: 'or',
      tip: 'Recommended: try it in the web app first, then connect your local tool with the tool-specific guide.',
      keyNotice: 'sk-your-key-here is a placeholder. Login, create a Key, and replace it before using the gateway.'
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
  const extension = selectedGuideOs.value === 'Windows' ? 'ps1' : 'env'
  const filename = `${siteDisplayName.value.toLowerCase().replace(/\s+/g, '-')}-config.example.${extension}`
  const blob = new Blob([guideScript.value], { type: 'text/plain;charset=utf-8' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = filename
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
