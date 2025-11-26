<template>
  <UiSectionWrapper class="py-20 md:py-32 relative bg-[#E3D5D4] text-bodyColor">
    <UiSectionContainer class="flex md:flex-row-reverse pb-8 md:pb-0">
      <div class="absolute bottom-0 right-0 z-20 max-h-[320px] md:max-h-[384px] lg:max-h-[480px]" :style="{ height: foregroundHeight }">
        <img :src="cta?.media?.product?.src" :alt="cta?.media?.product?.alt"
          class="h-full max-h-full max-w-full w-full object-contain" />
      </div>

      <div class="relative z-30 w-full h-full">
        <div class="flex flex-col h-full">
          <h2 v-motion :initial="{ opacity: 0, y: 32 }" :visible-once="{
            opacity: 1,
            y: 0,
            transition: {
              duration: 400,
              type: 'ease-in',
              stiffness: 250,
              damping: 25,
              mass: 1,
              delay: 100,
            },
          }" class="font-semibold text-[20px] md:text-[28px] lg:text-[32px]">
            {{ cta?.title }}
          </h2>
          <p v-motion :initial="{ opacity: 0, y: 32 }" :visible-once="{
            opacity: 1,
            y: 0,
            transition: {
              duration: 400,
              type: 'ease-in',
              stiffness: 250,
              damping: 25,
              mass: 1,
              delay: 150,
            },
          }" class="pt-5 text-[16px] md:text-[20px] lg:text-[28px] font-regular">
            {{ cta?.subtitle }}
          </p>

          <div class="flex flex-col lg:flex-row gap-2 lg:gap-7 pt-4 lg:pt-5">
            <UiButton v-motion :initial="{ opacity: 0, y: 32 }" :visible-once="{
              opacity: 1,
              y: 0,
              transition: {
                duration: 400,
                type: 'ease-in',
                stiffness: 250,
                damping: 25,
                mass: 1,
                delay: 200,
              },
            }" ghost decorative :width="isMobile ? '145px' : '320px'" :height="isMobile ? '24px' : '44px'"
              :font-size="isMobile ? '12' : '24'">{{ cta?.stats?.patients }}</UiButton>
            <UiButton v-motion :initial="{ opacity: 0, y: 32 }" :visible-once="{
              opacity: 1,
              y: 0,
              transition: {
                duration: 400,
                type: 'ease-in',
                stiffness: 250,
                damping: 25,
                mass: 1,
                delay: 250,
              },
            }" ghost decorative :width="isMobile ? '200px' : '320px'" :height="isMobile ? '24px' : '44px'"
              :font-size="isMobile ? '14' : '24'">{{ cta?.stats?.worldwideCare }}</UiButton>
          </div>

          <div class="pt-7 lg:pt-[50px] flex flex-col lg:flex-row gap-2 lg:gap-10 font-medium">
            <div v-for="(feature, index) in firstRowFeatures" :key="index" v-motion :initial="{ opacity: 0, y: 32 }" :visible-once="{
              opacity: 1,
              y: 0,
              transition: {
                duration: 400,
                type: 'ease-in',
                stiffness: 250,
                damping: 25,
                mass: 1,
                delay: 300 + (index * 50),
              },
            }" class="flex gap-1 items-center text-[12px] md:text-[20px] lg:text-[24px] font-medium">
              <UiIcon 
                :src="getIconPath(feature.iconType)" 
                :icon-color="feature.iconColor"
                size="w-[18px] h-[18px] md:w-[24px] md:h-[24px] lg:w-[28px] lg:h-[28px]"
                alt="star icon"
              />
              <p>{{ feature.text }}</p>
            </div>
          </div>
          <div v-if="secondRowFeatures.length > 0" class="mt-2 lg:mt-9 flex flex-col lg:flex-row gap-2 lg:gap-10">
            <div v-for="(feature, index) in secondRowFeatures" :key="index" v-motion :initial="{ opacity: 0, y: 32 }" :visible-once="{
              opacity: 1,
              y: 0,
              transition: {
                duration: 400,
                type: 'ease-in',
                stiffness: 250,
                damping: 25,
                mass: 1,
                delay: 300 + (index * 50),
              },
            }" class="flex gap-1 items-center text-[12px] md:text-[20px] lg:text-[24px]">
              <UiIcon 
                :src="getIconPath(feature.iconType)" 
                :icon-color="feature.iconColor"
                size="w-[18px] h-[18px] md:w-[24px] md:h-[24px] lg:w-[28px] lg:h-[28px]"
                alt="star icon"
              />
              <p>{{ feature.text }}</p>
            </div>
          </div>
        </div>
      </div>
    </UiSectionContainer>
  </UiSectionWrapper>


</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from "vue";
import { useSiteTextStore } from '~/stores/siteText';

const siteTextStore = useSiteTextStore();

// Initialize site text once for this component
const cta = computed(() => siteTextStore.getHomeText()?.cta);
const siteText = computed(() => siteTextStore.siteText);

// Get icon path from iconRegistry
const getIconPath = (iconType) => {
  return siteText.value?.iconRegistry?.[iconType] || '';
};

// Split features into two rows (first 2, then rest)
const firstRowFeatures = computed(() => {
  const features = cta.value?.features || [];
  return features.slice(0, 2);
});

const secondRowFeatures = computed(() => {
  const features = cta.value?.features || [];
  return features.slice(2);
});

// Responsive height for foreground image
const foregroundHeight = computed(() => {
  if (!cta.value?.media?.product?.heights) return '222px';
  if (isMobile.value) return cta.value.media.product.heights.mobile || '222px';
  return cta.value.media.product.heights.desktop || 'auto';
});

const isMobile = ref(false);
const checkMobile = () => {
  isMobile.value = window.innerWidth <= 768;
};

onMounted(() => {
  checkMobile();
  window.addEventListener("resize", checkMobile);
});

onUnmounted(() => {
  window.removeEventListener("resize", checkMobile);
});
</script>

<style scoped>
/* Component styles can be added here */
</style>
