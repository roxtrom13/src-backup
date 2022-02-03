<template lang="pug">
router-view(v-if="!loading" :key="$route.path")
</template>

<script>
import { ref } from "vue";
import { useStore } from "vuex";
import { ElLoading } from "element-plus";

let title = "./assets/" + import.meta.env.VITE_ASSETS_FOLDER + "/config";
let config = (await import(/* @vite-ignore */ title)).default;

export default {
  name: "App",
  setup() {
    const store = useStore();
    const loading = ref(true);

    const getFolderName = () => {
      return import.meta.env.VITE_ASSETS_FOLDER + "";
    }

    const hFavicon = () => {
      const favicon = document.getElementById("favicon");
      const iconSource = `/src/assets/${getFolderName()}/favicon.ico`;
      favicon.href = iconSource;
    }

    document.title = config.title + " - Estudio" || "Estudio";

    hFavicon();

    const updateStore = async () => {
      const userString = localStorage.getItem("user");
      // Global loading except for redirect component
      if (userString && location.pathname != "/redirect") {
        const elLoading = ElLoading.service({ text: "Cargando" });
        const userInfo = JSON.parse(userString);
        if (userInfo.access) {
          try {
            await store.dispatch("tokenLogin", userInfo.access);
            elLoading.close();
            loading.value = false;
          } catch {
            store.commit("CLEAR_USER_DATA");
          }
        } else {
          store.commit("CLEAR_USER_DATA");
        }
      }
      loading.value = false;
    };
    updateStore();

    return {
      loading,
    };
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  outline: none;
  font-family: Gilroy-Regular, Helvetica, Arial, sans-serif;
  font-smooth: grayscale;
}

:root {
  --gray: #7e8299;
  --green: #2ecc71;
  --smoke: #fefefe;
  --p: #647289;
  --client-primary: #3f3a64;
  --client-secondary: #20ad96;
  --client-green: #20ad96;
  --client-black: #333333;
}

.font-light {
  font-family: "Gilroy-Light", sans-serif;
}

.font-regular {
  font-family: "Gilroy-Regular", sans-serif;
}

.font-medium {
  font-family: "Gilroy-Medium", sans-serif;
}

.font-bold {
  font-family: "Gilroy-Bold", sans-serif;
}

.font-heavy {
  font-family: "Gilroy-Heavy", sans-serif;
}

.md-content {
  margin-top: 20px;
}

.label {
  color: var(--gray);
}

.search {
  margin-top: 10px;
}

.md-right-button {
  margin-bottom: 20px !important;
}

.el-upload--picture-card {
  background-color: white !important;
  border: none !important;
}

.is-required {
  font-weight: bold;
}

.video-link {
  font-weight: bold;
}

.el-menu-item-group__title {
  padding-top: 0px !important;
  padding-bottom: 0px !important;
}

.el-button {
  min-height: 40px;
  min-width: 40px;
}

.el-drawer__header {
  margin-bottom: 0px !important;
}

.asset-card__link {
  span {
    margin-left: 10px;
    margin-top: 4px;
  }
}

.el-form-item__label {
  font-size: 16px !important;
}
</style>
