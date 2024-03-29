<template>
  <div :class="containerClass"
       @click="onWrapperClick">
    <AppTopBar @menu-toggle="onMenuToggle"/>
    <div class="layout-sidebar"
         @click="onSidebarClick">
      <AppMenu :model="menu"
               @menuitem-click="onMenuItemClick"/>
    </div>

    <div class="layout-main-container">
      <div class="layout-main">
        <router-view/>
      </div>
      <AppFooter/>
    </div>

    <transition name="layout-mask">
      <div v-if="mobileMenuActive"
           class="layout-mask p-component-overlay"></div>
    </transition>
  </div>
</template>

<script>
import AppTopBar from './AppTopbar.vue';
import AppMenu from './AppMenu.vue';
import AppFooter from './AppFooter.vue';

export default {
  data()
  {
    return {
      layoutMode: 'static',
      layoutColorMode: 'light',
      staticMenuInactive: false,
      overlayMenuActive: false,
      mobileMenuActive: false,
      menu: [
        {
          label: 'Inicio',
          items: [{
            label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/'
          }]
        },
        {
          label: 'Pinos',
          items: [
            {label: 'Cadastro', icon: 'pi pi-fw pi-plus', to: '/cadastro'},
            {label: 'Valores', icon: 'pi pi-fw pi-list', to: '/valores'},
          ]
        },
      ]
    }
  },
  watch: {
    $route()
    {
      this.menuActive = false;
      this.$toast.removeAllGroups();
    }
  },
  methods: {
    onWrapperClick()
    {
      if (!this.menuClick)
      {
        this.overlayMenuActive = false;
        this.mobileMenuActive = false;
      }

      this.menuClick = false;
    },
    onMenuToggle()
    {
      this.menuClick = true;

      if (this.isDesktop())
      {
        if (this.layoutMode === 'overlay')
        {
          if (this.mobileMenuActive === true)
          {
            this.overlayMenuActive = true;
          }

          this.overlayMenuActive = !this.overlayMenuActive;
          this.mobileMenuActive = false;
        }
        else if (this.layoutMode === 'static')
        {
          this.staticMenuInactive = !this.staticMenuInactive;
        }
      }
      else
      {
        this.mobileMenuActive = !this.mobileMenuActive;
      }

      event.preventDefault();
    },
    onSidebarClick()
    {
      this.menuClick = true;
    },
    onMenuItemClick(event)
    {
      if (event.item && !event.item.items)
      {
        this.overlayMenuActive = false;
        this.mobileMenuActive = false;
      }
    },
    addClass(element, className)
    {
      if (element.classList)
      {
        element.classList.add(className);
      }
      else
      {
        element.className += ' ' + className;
      }
    },
    removeClass(element, className)
    {
      if (element.classList)
      {
        element.classList.remove(className);
      }
      else
      {
        element.className = element.className.replace(new RegExp('(^|\\b)' + className.split(' ').join('|') + '(\\b|$)', 'gi'), ' ');
      }
    },
    isDesktop()
    {
      return window.innerWidth >= 992;
    },
  },
  computed: {
    containerClass()
    {
      return ['layout-wrapper', {
        'layout-overlay': this.layoutMode === 'overlay',
        'layout-static': this.layoutMode === 'static',
        'layout-static-sidebar-inactive': this.staticMenuInactive && this.layoutMode === 'static',
        'layout-overlay-sidebar-active': this.overlayMenuActive && this.layoutMode === 'overlay',
        'layout-mobile-sidebar-active': this.mobileMenuActive,
        'p-input-filled': this.$primevue.config.inputStyle === 'filled',
        'p-ripple-disabled': this.$primevue.config.ripple === false,
        'layout-theme-light': this.$appState.theme.startsWith('saga')
      }];
    },
  },
  beforeUpdate()
  {
    if (this.mobileMenuActive)
    {
      this.addClass(document.body, 'body-overflow-hidden');
    }
    else
    {
      this.removeClass(document.body, 'body-overflow-hidden');
    }
  },
  components: {
    'AppTopBar': AppTopBar,
    'AppMenu': AppMenu,
    'AppFooter': AppFooter,
  }
}
</script>

<style lang="scss">
@import './App.scss';
</style>
