<template>
  <div class="about">
    <div class="top-wrapper">
      <div class="theme" @click="changeTheme">
        <svg class="theme__icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none"
          stroke="var(--primary-text)" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <circle cx="12" cy="12" r="5"></circle>
          <line x1="12" y1="1" x2="12" y2="3"></line>
          <line x1="12" y1="21" x2="12" y2="23"></line>
          <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
          <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
          <line x1="1" y1="12" x2="3" y2="12"></line>
          <line x1="21" y1="12" x2="23" y2="12"></line>
          <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
          <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
        </svg>
      </div>
      <Socials />
    </div>

    <div class="info">
      <div class="logo">
        <svg class="logo__svg" viewBox="0 0 125 106" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M10.5 10.6164H25.9395L72.0591 79.7985L62.5 95.3836L10.5 10.6164Z" fill="var(--accent-color)" />
          <path
            d="M47.3184 20.2779H92.6295L77.7628 42.5797L69.975 31.4046H54.442L79.3309 68.2964L114.5 10.6164H40.8773L47.3184 20.2779Z"
            fill="var(--accent-color)" />
        </svg>

      </div>

      <span class="name">Vladimir Pakhmutov</span>
      <span class="vacancy"><span class="front">front</span>end developer</span>
    </div>

    <div class="separator">find your&nbsp;<span class="vibe">vibe</span></div>
  </div>
</template>

<script setup lang="ts">
import { useThemeStore } from '../store'
import Socials from './Socials.vue'

const changeTheme = () => {
  const theme = localStorage.getItem('theme') || 'light'

  if (theme === 'light') {
    localStorage.setItem('theme', 'dark')
    document.documentElement.dataset.theme = 'dark'
    useThemeStore().setTheme('dark')
  } else {
    localStorage.setItem('theme', 'light')
    document.documentElement.dataset.theme = 'light'
    useThemeStore().setTheme('light')
  }
}
</script>

<style lang="scss" scoped>
.about {
  display: flex;
  flex-direction: column;
}

.top-wrapper {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.info {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 8px;
}

.theme {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: var(--opacity-background);
  padding: 8px;
  border-radius: 50%;
  cursor: pointer;
  transition: background-color 200ms;

  &:hover {
    background-color: rgba(0, 0, 0, 0.1);

    [data-theme='dark'] & {
      background-color: rgba(255, 255, 255, 0.1);
    }
  }

  &__icon {
    width: 24px;
    height: 24px;
  }
}

.logo {
  display: flex;
  align-items: center;
  justify-content: center;

  &__svg {
    cursor: pointer;
    width: 128px;
    height: 128px;
    -webkit-filter: drop-shadow(0 0 4px var(--accent-color));
    filter: drop-shadow(0 0 4px var(--accent-color));
    transition: filter 300ms;

    &:active {
      transition: transform 100ms;
      transform: scale(1.04);
    }

    &:hover {
      -webkit-filter: drop-shadow(0 0 8px var(--accent-color));
      filter: drop-shadow(0 0 12px var(--accent-color));
    }
  }
}

.name {
  font-family: 'Poppins';
  text-transform: uppercase;
  font-weight: 500;
  font-size: 48px;
  color: var(--primary-text);
  line-height: 1.15;
  letter-spacing: 0.8px;
  user-select: none;
}

.vacancy {
  font-family: 'Consolas';
  color: var(--secondary-text);
  font-weight: 400;
  font-size: 36px;
  line-height: 1.15;
  user-select: none;
  letter-spacing: 0.8px;
}

.front {
  color: var(--accent-color);
  cursor: crosshair;
  display: inline-flex;
  transition: transform 500ms;

  &:hover {
    transform: rotate3d(0, 1, 0, 9rad);
  }
}

.separator {
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Roboto';
  font-weight: 300;
  letter-spacing: 0.8px;
  padding: 48px;
  font-size: 32px;
  color: var(--tertiary-text);
  user-select: none;
}

.vibe {
  color: var(--accent-color);
  transition: font-weight 300ms;
  cursor: pointer;

  &:hover {
    font-weight: 500;
  }
}

@media screen and (max-width: 768px) {
  .about {
    gap: 48px;
  }

  .separator {
    padding: 4px 24px;
  }
}

@media screen and (max-width: 480px) {
  .vacancy {
    font-size: 32px;
  }

  .about {
    gap: 64px;
  }
}
</style>