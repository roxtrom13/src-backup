<script lang="ts">
import { defineComponent, reactive, ref } from "vue";
import Divider from "../../components/utils/Divider.vue";
import { useStore } from "vuex";
import { useRouter } from "vue-router";
import { ElMessage } from "element-plus";

export default defineComponent({
  setup() {
    const store = useStore();

    const router = useRouter();

    const isLoading = ref(false);

    const user = reactive({
      username: "",
      password: "",
    });

    const showError = (errMessage: string) => {
      ElMessage.error(errMessage);
    };

    const handleLogin = async () => {
      isLoading.value = true;

      try {
        await store.dispatch("performLogin", user);
        if (store.getters.isAdmin) {
          router.push({ name: "Courses" });
        } else {
          router.push({ name: "Users" });
        }
      } catch (e: any) {
        showError(e.response.data.detail);
      } finally {
        isLoading.value = false;
      }

    };

    return { user, handleLogin, isLoading };
  },
  components: {
    Divider,
  },
});
</script>

<template lang="pug">
.login-container
  el-row(justify="center")
    el-col(:xs="18" :sm="14" :md="10" :lg="6")
      h1.mb-30 Inicio de sesión
      el-card.card-box
        el-row
          el-form(ref="form" :model="user" label-position="top" style="width: 100%;")
            el-form-item(label="Usuario")
              el-input(v-model="user.username" label="Usuario")
            el-form-item(label="Constraseña")
              el-input(v-model="user.password" show-password @keyup.enter="handleLogin")
            el-form-item
              Divider
              el-row(class="block" justify="center")
                el-button(type="primary" @click="handleLogin" style="width: 100%;" :loading="isLoading")
                  span(v-if="!isLoading") Iniciar sesión
                  span(v-else) Iniciando sesión

</template>

<style scoped>
.login-container {
  margin-top: 60px;
}
.card-box {
  padding: 10px 15px 10px 15px;
}

.mb-30 {
  margin-bottom: 30px;
}

.mt-10 {
  margin-top: 10px;
}

.block {
  width: 100%;
}
</style>
