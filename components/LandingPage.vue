<script setup lang="ts">
import type { ParsedContent } from "@nuxt/content";

defineProps<{
  page: ParsedContent | null;
}>();

const SECTION_SLOT_WIDTHS: Record<string, string> = {
  video: "max-w-4xl",
  pricing: "max-w-5xl",
};
</script>

<template>
  <div v-if="page">
    <ULandingHero :orientation="page.hero.orientation" :links="page.hero.links">
      <template #headline>
        <UBadge
          v-if="page.hero.headline"
          variant="subtle"
          size="md"
          class="hover:bg-primary-100 dark:bg-primary-950/100 dark:hover:bg-primary-900 relative rounded-full font-medium shadow-none"
        >
          <NuxtLink
            :to="page.hero.headline.to"
            class="focus:outline-none"
            tabindex="-1"
          >
            <span class="absolute inset-0" aria-hidden="true" />
          </NuxtLink>

          <span class="flex items-center gap-1">
            <UIcon
              v-if="page.hero.headline.icon"
              :name="page.hero.headline.icon"
              class="pointer-events-none h-4 w-4"
            />
            {{ page.hero.headline.label }}
          </span>
        </UBadge>
      </template>

      <template #title>
        <span v-html="page.hero.title" />
      </template>

      <template #description>
        <span v-html="page.hero.description" />
      </template>

      <template #default>
        <ElementVideo v-bind="page.hero.video" />
      </template>
    </ULandingHero>

    <ULandingSection
      v-for="(section, index) of page.sections"
      :id="section.slot"
      :key="index"
      :ui="{
        container: SECTION_SLOT_WIDTHS[section.slot],
      }"
      v-bind="section"
      class="!pt-16"
    >
      <template v-if="section.title" #title>
        <span v-html="section.title" />
      </template>

      <template v-if="section.description" #description>
        <span v-html="section.description" />
      </template>

      <template #video>
        <ElementVideo v-bind="section.video" />
      </template>

      <template #features>
        <ULandingGrid>
          <ULandingCard
            v-for="(card, subIndex) of section.cards"
            :key="subIndex"
            v-bind="card"
          />
        </ULandingGrid>
      </template>

      <template #pricing>
        <div
          class="bg-primary-700 flex flex-col gap-16 rounded-xl px-4 py-6 shadow sm:gap-y-24 sm:p-16 md:grid md:grid-cols-2 md:items-end"
        >
          <div>
            <slot name="image-pricing" />
          </div>
          <UPricingCard
            v-bind="section.plan"
            :ui="{
              highlight: 'ring-1 ring-primary-600 dark:ring-primary-200',
            }"
          />
        </div>
      </template>

      <template #faq>
        <ULandingFAQ :items="section.items" multiple class="mx-auto max-w-4xl">
          <template #item="{ item }">
            <MDC
              :value="item.content"
              class="prose prose-primary dark:prose-invert max-w-none text-gray-500 dark:text-gray-400"
            />
          </template>
        </ULandingFAQ>
      </template>
    </ULandingSection>

    <ULandingSection class="!pt-0">
      <ULandingCTA
        v-bind="page.cta"
        align="right"
        card
        :ui="{
          wrapper: 'bg-primary-700 dark:bg-primary-700 ring-0',
          title: 'text-5xl !text-rhino-100 sm:text-7xl',
          description: '!text-rhino-100',
        }"
      >
        <template #title>
          <span v-html="page.cta?.title" />
        </template>

        <slot name="image-cta" />
      </ULandingCTA>
    </ULandingSection>
  </div>
</template>
