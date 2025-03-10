<script setup lang="ts">
import type { ParsedContent } from "@nuxt/content";
import { withQuery } from "ufo";

defineProps<{
  page: ParsedContent | null;
}>();

const SECTION_SLOT_WIDTHS: Record<string, string> = {
  video: "max-w-4xl",
};

const appConfig = useAppConfig();

const licenseHolder = ref("");

function getUrlWithLicensee(url: string) {
  return withQuery(url, {
    "checkout[custom][licenseHolder]": licenseHolder.value || undefined,
  });
}
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
              :class="page.hero.headline.iconClass"
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
        container: SECTION_SLOT_WIDTHS[section.slot] || 'max-w-6xl',
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
          class="bg-primary-700 flex flex-col gap-16 rounded-xl px-4 py-6 shadow sm:gap-y-24 sm:p-16 md:grid md:grid-cols-[2fr,1fr] md:items-end lg:grid-cols-2"
        >
          <div>
            <slot name="image-pricing" />
          </div>

          <div class="relative">
            <UPricingCard
              v-bind="section.plan"
              :button="{
                ...section.plan.button,
                to: getUrlWithLicensee(section.plan.button.to),
              }"
              :ui="{
                highlight: 'ring-1 ring-primary-600 dark:ring-primary-200',
              }"
            >
              <template #features>
                <ul
                  v-if="section.plan.features?.length"
                  class="mb-6 space-y-3 text-sm"
                >
                  <li
                    v-for="(offer, offerIndex) of section.plan.features"
                    :key="offerIndex"
                    class="flex min-w-0 items-center gap-x-3"
                  >
                    <UIcon
                      :name="appConfig.ui.icons.check"
                      class="text-primary h-5 w-5 flex-shrink-0"
                    />

                    <span class="truncate text-gray-600 dark:text-gray-400">{{
                      offer
                    }}</span>
                  </li>
                </ul>

                <UDivider label="Licensee" class="mb-6" />

                <UInput
                  v-model="licenseHolder"
                  color="gray"
                  placeholder="License holder"
                  class="mb-2"
                />
                <div class="text-sm text-gray-500 dark:text-gray-400">
                  Who will own this license (e.g. your full name, organization,
                  or client)? Will be you by default.
                </div>
              </template>
            </UPricingCard>

            <div
              class="absolute inset-x-0 top-0 flex translate-y-[-50%] justify-center"
            >
              <UButton
                size="2xs"
                variant="outline"
                to="https://hub.kirby.tools"
                :ui="{
                  rounded: 'rounded-full',
                  variant: {
                    outline: 'bg-white dark:bg-gray-900',
                  },
                }"
              >
                50% off for Returning Customers
              </UButton>
            </div>
          </div>
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

    <ULandingSection
      class="!pt-0"
      :ui="{
        container: 'max-w-6xl',
      }"
    >
      <ULandingCTA
        v-bind="page.cta"
        align="right"
        card
        :ui="{
          wrapper: 'bg-primary-700 dark:bg-primary-700 ring-0',
          title: 'text-5xl !text-white sm:text-7xl',
          description: '!text-white',
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
