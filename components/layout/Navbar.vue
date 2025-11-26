<template>
  <nav :class="[
    'w-full fixed top-0 z-50 flex justify-center shadow-lg',
    props.color || 'bg-white'
  ]">
    <div v-motion :initial="{ opacity: 0.3, y: 8 }" :visible-once="{
      opacity: 1,
      y: 0,
      transition: {
        duration: 400,
        type: 'ease-in',
        stiffness: 250,
        damping: 25,
        mass: 1,
      },
    }"       :class="[
        'lg:max-w-[1328px] w-full flex justify-center px-4 md:px-8 pb-2 md:pb-0',
        props.hideNavigation ? 'justify-center' : 'justify-between',
      ]"
      :style="{ height: navbarHeight }">
      <!-- Mobile hamburger menu on the left -->
      <button v-if="!props.hideNavigation" class="md:hidden rounded" :aria-label="common?.accessibility?.toggleMenu" @click="toggleMobileMenu">
        <img :src="common?.media?.hamburgerMenu?.src" :alt="common?.accessibility?.menu" class="h-4 w-4" />
      </button>

      <!-- Logo on center-->
      <div class="flex items-center">
        <NuxtLink to="/">
          <img :src="navbarLogoSrc" :alt="navbarLogoAlt" class="w-auto" :style="{ height: logoHeight }" />
        </NuxtLink>
      </div>
      <div class="flex items-center gap-10">

        <!-- Navigation links in middle -->
        <div v-if="!props.hideNavigation" class="hidden md:flex items-center gap-8">
          <NuxtLink to="/about">
            <p class="text-white text-lg font-medium">{{ common?.navigation?.about }}</p>
          </NuxtLink>
          <NuxtLink to="/contact">
            <p class="text-white text-lg font-medium">{{ common?.navigation?.contact }}</p>
          </NuxtLink>
          <NuxtLink to="/products">
            <p class="text-white text-lg font-medium">{{ common?.navigation?.product }}</p>
          </NuxtLink>
        </div>

        <!-- Get Started button on right -->
        <div v-if="!props.hideNavigation" class="flex flex-row items-center md:gap-3 relative">
          <NuxtLink to="/consultation" class="hidden md:block">
            <UiButton background-color="white" text-color="accentColor1" width="176px" height="32px" font-size="20">
              {{ common?.buttons?.getStarted }}
            </UiButton>
          </NuxtLink>
          <NuxtLink to="/consultation" class="block md:hidden">
            <UiButton background-color="white" text-color="accentColor1" width="144px" height="28px" font-size="18">
              {{ common?.buttons?.getStarted }}
            </UiButton>
          </NuxtLink>

          <!-- Mobile dropdown menu -->
          <div v-if="isMobileMenuOpen"
            class="lg:hidden absolute top-full right-36 mt-2 w-48 bg-white border border-gray-200 rounded-lg shadow-lg z-50">
            <div class="py-2">
              <NuxtLink to="/about" class="block px-4 py-2 text-black text-lg font-medium hover:bg-gray-100"
                @click="closeMobileMenu">
                {{ common?.navigation?.about }}
              </NuxtLink>
              <NuxtLink to="/contact" class="block px-4 py-2 text-black text-lg font-medium hover:bg-gray-100"
                @click="closeMobileMenu">
                {{ common?.navigation?.contact }}
              </NuxtLink>
              <NuxtLink to="/products" class="block px-4 py-2 text-black text-lg font-medium hover:bg-gray-100"
                @click="closeMobileMenu">
                {{ common?.navigation?.product }}
              </NuxtLink>
            </div>
          </div>
        </div>
      </div>

    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from "vue";
import { useSiteTextStore } from "~/stores/siteText";

const siteTextStore = useSiteTextStore();
const route = useRoute();
const common = computed(() => siteTextStore.getCommonText());

// Define the color prop with a default value of 'bg-white'
const props = defineProps({
  color: {
    type: String,
    default: "bg-white",
  },
  hideNavigation: {
    type: Boolean,
    default: false,
  },
});

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

const navbarLogo = computed(() => getPageLayout.value?.navbar?.logo);
const navbarLogoSrc = computed(() => navbarLogo.value?.src || "/assets/images/brand/logo.svg");
const navbarLogoAlt = computed(() => navbarLogo.value?.alt || common.value?.accessibility?.brandLogo || "Brand logo");

// Mobile detection
const isMobile = ref(false);

// Responsive heights
const navbarHeight = computed(() => {
  const layout = getPageLayout.value;
  if (!layout?.navbar?.height) return '83px';
  if (isMobile.value) return layout.navbar.height.mobile || '83px';
  return layout.navbar.height.desktop || '68px';
});

const logoHeight = computed(() => {
  const layout = getPageLayout.value;
  if (!layout?.navbar?.logo?.height) return '24px';
  if (isMobile.value) return layout.navbar.logo.height.mobile || '24px';
  if (window.innerWidth >= 768 && window.innerWidth < 1024) {
    return layout.navbar.logo.height.tablet || '28px';
  }
  return layout.navbar.logo.height.desktop || '28px';
});

// Mobile menu state
const isMobileMenuOpen = ref(false);

// Toggle mobile menu
const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value;
};

// Close mobile menu
const closeMobileMenu = () => {
  isMobileMenuOpen.value = false;
};

// Close mobile menu when clicking outside
const handleClickOutside = (event) => {
  if (isMobileMenuOpen.value && !event.target.closest("nav")) {
    closeMobileMenu();
  }
};

// Check mobile on mount and resize
const checkMobile = () => {
  isMobile.value = window.innerWidth <= 768;
};

// Add click outside listener
onMounted(() => {
  document.addEventListener("click", handleClickOutside);
  checkMobile();
  window.addEventListener("resize", checkMobile);
});

onUnmounted(() => {
  document.removeEventListener("click", handleClickOutside);
  window.removeEventListener("resize", checkMobile);
});
</script>

<style scoped>
/* Custom styles if needed */
</style>
