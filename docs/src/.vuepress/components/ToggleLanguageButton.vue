<script setup>
import { ref, watch } from "vue";
import { useRoute, useRouter } from 'vue-router'
import { getLanguagePath, getCurrentLanguage, languages } from '../composables/useI18N';
import { useThemeLocaleData } from "@vuepress/theme-default/lib/client/composables/index.js";

defineEmits(["toggle"]);

const route = useRoute();
const themeLocale = useThemeLocaleData();

const isDropdownOpen = ref(false);
const linkItems = ref({
  English: "/",
  Español: "/es/"
});

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value;
};

watch(() => route.path, (path) => {
  // update RouterLink destination after path change
  const links = {};
  for (let lang in languages) {
    const newPath = getLanguagePath(path, lang, getCurrentLanguage(path));
    links[lang] = newPath;
  }
  linkItems.value = links;

  // collapse dropdown after path change
  isDropdownOpen.value = false;
}, { immediate: true });
</script>

<template>
  <div class="cookbook-translate-button">
    <div
      class="toggle-button"
      :title="themeLocale.toggleTranslate"
      role="button"
      tabindex="0"
      @click="toggleDropdown"
    >
      Language
    </div>
    <ul v-if="isDropdownOpen" class="language-dropdown">
      <RouterLink
        v-for="lang in Object.keys(languages)"
        :key="lang"
        :to="linkItems[lang]">
        {{ lang }}
      </RouterLink>
    </ul>
  </div>
</template>

<style lang="scss">
.cookbook-translate-button {
  position: relative;
  display: inline-block;
  font-weight: 500;
}

.toggle-button {
  cursor: pointer;
}

.toggle-button:hover {
  text-decoration: underline;
}

.language-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  margin-top: 5px;
  padding: 5px;
  color: #000;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 4px;
  list-style: none;
  z-index: 1;
  display: inline-block;

  & > a {
    display: block;
    padding: 5px;

    &:hover {
      background-color: #f5f5f5;
    }
  }
}
</style>
