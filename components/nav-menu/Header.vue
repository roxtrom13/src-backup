<script lang="ts">
import { defineComponent, ref, computed, reactive, toRefs } from "vue";
import { useStore } from "vuex";

import { CaretBottom, CaretTop, User, CircleClose } from "@element-plus/icons";
import "element-plus/theme-chalk/display.css";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faBars } from "@fortawesome/free-solid-svg-icons";

import { getAvatar } from "../../services/ui-avatar";

export default defineComponent({
  setup() {
    const store = useStore();
    const droped = ref(false);

    const state = reactive({
      drawer: false,
    });


    const mainLogo = () => {
      const folder = import.meta.env.VITE_ASSETS_FOLDER as string;
      return "../../src/assets/" + folder + "/logo.png";
    }

    const isLoggedIn = store.getters.loggedIn;

    const name = isLoggedIn ? store.state.user.username : "";

    const user = computed(() => {
      return store.getters.getUser;
    });

    const isAdmin = () => {
      return store.getters.isAdmin;
    }

    const handleLogout = () => {
      store.dispatch("logout");
    };

    const menuItems = computed(() => {
      if (isAdmin()) {
        return store.state.adminMenu;
      } else {
        return store.state.sellerMenu;
      }
    });

    return {
      ...toRefs(state),
      droped,
      name,
      isLoggedIn,
      handleLogout,
      menuItems,
      getAvatar,
      user,
      faBars,
      mainLogo,
    };
  },
  components: {
    CaretBottom,
    CaretTop,
    User,
    CircleClose,
    FontAwesomeIcon,
  },
});
</script>

<template lang="pug">
.main-container
  .header-section
    .header-inner
      .container.position-relative
        .menu-items-container
          .logo
            img.main-icon(:src="mainLogo()")
          .navigation.hidden-md-and-down
            nav.site-main-menu
              ul
                li.position-static(v-for="item in menuItems" :key="item.route")
                  a(:href="item.route") {{ item.name }}
          .user-icon
            el-dropdown(
              trigger="hover"
              :show-timeout="0"
              :hide-timeout="250"
              @visible-change="droped = !droped"
            )
              el-avatar.user-avatar(:src="getAvatar(user.first_name, user.last_name)")
              template(#dropdown)
                el-dropdown-menu
                  el-dropdown-item(@click="$router.push('/profile')")
                    el-icon
                      user
                    span Mi perfil
                  el-dropdown-item(@click="handleLogout")
                    el-icon
                      circle-close
                    span Cerrar sesión
            el-button.mobile-menu.hidden-md-and-up(type="text" @click="drawer = true")
              FontAwesomeIcon.mobile-menu__icon(:icon="faBars" size="lg")

            el-drawer(v-model="drawer" size="340px" direction="ltr")
              template.drawer-title(#title)
                .logo.logo__fixed
                  img.main-icon(:src="mainLogo()")
              el-divider
              el-row.drawer__content
                ul
                  div(v-for="item in menuItems" :key="item.route")
                    li
                      a(:href="item.route") {{ item.name }}

</template>

<style lang="scss" scoped>
.main-container {
  box-sizing: border-box;
}

.header-section {
  min-height: 80px;
}

.header-inner {
  z-index: 99;
  display: flex;
  align-items: center;
  min-height: 80px;
  background-color: #fff;
  border-bottom: solid 1px #e6e6e6;
}

.container {
  max-width: 1300px;
  width: 100%;
  padding-right: 15px;
  padding-left: 15px;
  margin-right: auto;
  margin-left: auto;
}

.position-relative {
  position: relative !important;
}

.menu-items-container {
  display: flex;
  flex-wrap: wrap;
  margin-right: -15px;
  margin-left: -15px;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
}

.main-icon {
  width: 50px;
  margin-top: 12px;
}

.logo {
  flex: 0 0 auto;
  width: auto;
  max-width: 100%;
  padding-right: 15px;
  padding-left: 15px;
}

.logo__fixed {
  margin-top: -15px;
  margin-bottom: -25px;
}

.navigation {
  position: static;
  display: block;
  flex-basis: 0;
  flex-grow: 1;
  max-width: 100%;
  width: 100%;
  padding-right: 15px;
  padding-left: 15px;
}

.site-main-menu {
  display: flex;
  flex-wrap: wrap;
  justify-content: left;

  ul {
    display: flex;
    flex-wrap: wrap;
    margin: 0;
    padding-left: 0;
    list-style: none;
    li {
      a {
        font-size: 17px;
        font-weight: 500;
        line-height: 1.375;
        display: block;
        padding: 29px 17px;
        color: #3f3a64;
        text-decoration: none;
      }
    }
  }
}

.position-static {
  position: static !important;
}

.user-name {
  height: 100%;
  vertical-align: middle;
  font-weight: 500;
  color: var(--gray);
  padding-right: 15px;
  font-size: 17px;
  margin-bottom: 8px;
}

.user {
  font-size: 14px;
  font-weight: 600;
  line-height: 60px;
}

.caret-icon {
  padding-top: 15px;
  font-size: 18px;
}

.user-avatar {
  height: 30px;
  width: 30px;
}

.mobile-menu {
  margin-left: 20px;
  color: #3f3a64;
}

.menu-drawer {
  background-color: black !important;
  width: 800px !important;
}

.drawer__content {
  margin: 20px;
  ul {
    list-style: none;
    div {
      border-bottom: solid 1px #3f3a64;
      width: 260px;
      margin-bottom: 20px;
      li {
        margin-bottom: 20px;
        a {
          text-decoration: none;
          font-size: 17px;
        }
      }
    }
  }
}
</style>
