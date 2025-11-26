<template>
  <footer
    class="bg-[#E3D5D4] flex justify-center pb-4"
    :style="{ height: footerHeight }"
  >
    <div
      class="max-w-[1328px] w-full flex items-end justify-between md:justify-center px-4 md:px-8 md:gap-8"
    >
      <div class="flex items-center">
        <NuxtLink to="/">
          <div class="pb-1" :style="{ height: logoHeight }">
            <img
              :src="footerLogoSrc"
              :alt="footerLogoAlt"
              class="h-full w-full"
            />
          </div>
        </NuxtLink>
      </div>
      <div class="md:block h-[1px] mb-1.5 w-full mx-2 md:mx-5 flex-1 bg-accentColor1"></div>

      <!-- Navigation buttons on the right -->
      <div class="flex items-center gap-x-2 md:gap-x-6">
        <NuxtLink
          to="/about"
          class="text-accentColor1 text-[14px] md:text-[18px] lg:text-[20px] transition-colors duration-200"
        >
          {{ common?.navigation?.about }}
        </NuxtLink>
        <NuxtLink
          to="/contact"
          class="text-accentColor1 text-[14px] md:text-[18px] lg:text-[20px] transition-colors duration-200"
        >
          {{ common?.navigation?.contactUs }}
        </NuxtLink>
        <NuxtLink
          to="/products"
          class="text-accentColor1 text-[14px] md:text-[18px] lg:text-[20px] transition-colors duration-200"
        >
          {{ common?.navigation?.products }}
        </NuxtLink>
      </div>
    </div>
    <!-- Logo on the left -->
  </footer>
</template>

<script setup>
import { computed, ref, onMounted, onUnmounted } from "vue";
import { useSiteTextStore } from "~/stores/siteText";

const siteTextStore = useSiteTextStore();
const route = useRoute();
const common = computed(() => siteTextStore.getCommonText());

// Get current page name from route
const getCurrentPageName = () => {
  const path = route.path;
  if (path === '/') return 'home';
  if (path.startsWith('/about')) return 'about';
  if (path.startsWith('/products')) return 'products';
  if (path.startsWith('/contact')) return 'contact';
  return null; // Return null for other pages to use common defaults
};

// Get page-specific layout or fall back to common
const getPageLayout = computed(() => {
  const pageName = getCurrentPageName();
  const pageData = pageName ? siteTextStore.siteText?.[pageName] : null;
  return pageData?.layout || siteTextStore.siteText?.home?.layout || null;
});

const footerLogo = computed(() => getPageLayout.value?.footer?.logo);
const footerLogoSrc = computed(() => footerLogo.value?.src || "/assets/images/brand/logo-alt.svg");
const footerLogoAlt = computed(() => footerLogo.value?.alt || common.value?.accessibility?.brandLogo || "Brand logo");

// Mobile detection
const isMobile = ref(false);
const checkMobile = () => {
  isMobile.value = window.innerWidth <= 768;
};

// Responsive heights
const footerHeight = computed(() => {
  const layout = getPageLayout.value;
  if (!layout?.footer?.height) return '64px';
  if (isMobile.value) return layout.footer.height.mobile || '64px';
  return layout.footer.height.desktop || '72px';
});

const logoHeight = computed(() => {
  const layout = getPageLayout.value;
  if (!layout?.footer?.logo?.height) return '32px';
  if (isMobile.value) return layout.footer.logo.height.mobile || '20px';
  return layout.footer.logo.height.desktop || '32px';
});

onMounted(() => {
  checkMobile();
  window.addEventListener("resize", checkMobile);
});

onUnmounted(() => {
  window.removeEventListener("resize", checkMobile);
});
</script>

<style scoped>
/* Custom styles if needed */
</style>
