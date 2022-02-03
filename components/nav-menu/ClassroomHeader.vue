<script lang="ts">
import { defineComponent, reactive, toRefs, computed } from "vue";
import { useStore } from "vuex";
import { useRoute } from "vue-router";

import { UserFilled, Notebook, CircleClose } from "@element-plus/icons";

import { getAvatar } from "../../services/ui-avatar";
import SideOption from "../../components/nav-menu/SideOption.vue";
import BackButton from "../BackButton.vue";

export default defineComponent({
  setup() {
    const state = reactive({
      drawer: false,
    });
    const route = useRoute();
    const store = useStore();
    const handleClose = (done: any) => {
      done();
    }

    const isInClassroom = () => {
      return route.path === "/classroom";
    }

    const user = computed(() => {
      return store.getters.getUser;
    });

    const hLogout = () => {
      store.dispatch('logout');
    }

    const mainLogo = () => {
      const folder = import.meta.env.VITE_ASSETS_FOLDER as string;

      return "../../../src/assets/" + folder + "/logo.png";
    }

    return {
      ...toRefs(state),
      user,
      getAvatar,
      handleClose,
      hLogout,
      isInClassroom,
      store,
      mainLogo,
    };
  },
  components: {
    SideOption,
    BackButton,
    UserFilled,
    Notebook,
    CircleClose,
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
            BackButton.hidden-sm-and-down(
              style="margin-left: 10%;"
              path="/classroom"
              v-if="!isInClassroom()"
            )
              span.back-button {{ store.state.course.name }}
          .user-icon
            el-avatar.user-avatar(:src="getAvatar(user.first_name, user.last_name)" @click="drawer = true")

            el-drawer(v-model="drawer" size="310px" direction="rtl")
              template.drawer-title(#title)
                .logo.logo__fixed
                  img.main-icon(:src="mainLogo()")
              el-divider
              el-row
                SideOption.side-option(
                  title="Mi Perfil"
                  label="Ir a la configuración de tu perfil"
                  @click="$router.push('/classroom/profile')"
                )
                  //i.el-icon-user-solid
                  el-icon
                    UserFilled
                SideOption.side-option(
                  title="Mis Cursos"
                  label="Ir a ver tus cursos disponibles"
                  @click="$router.push('/classroom')"
                )
                  //i.el-icon-notebook-2
                  el-icon
                    Notebook
                SideOption.side-option(title="Cerrar Sesión" @click="hLogout")
                  i.el-icon-circle-close
                  el-icon
                    CircleClose

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

.menu-items-container {
  display: flex;
  flex-wrap: wrap;
  margin-left: -15px;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
}

.user-avatar {
  height: 30px;
  width: 30px;
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

.back-button {
  font-family: Gilroy-Regular;
  font-weight: 600;
  font-size: 18px;
  color: var(--client-black);
}

.side-option {
  margin-bottom: 9px;
}
</style>

