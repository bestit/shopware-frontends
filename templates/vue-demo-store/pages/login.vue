<script setup lang="ts">
defineOptions({
  name: "LoginPage",
});

const { push } = useRouter();
const { isLoggedIn } = useUser();

const redirectAfterLogin = (path = "/account") => push(path);
const localePath = useLocalePath();
const { formatLink } = useInternationalization(localePath);

onBeforeMount(async () => {
  if (import.meta.client && isLoggedIn.value) {
    // redirect to account page if user is logged in
    navigateTo({ path: "/account" });
  }
});

useBreadcrumbs([
  {
    name: "Login",
    path: "/login",
  },
]);
</script>

<template>
  <LayoutBreadcrumbs />
  <div class="login-wrapper">
    <AccountLoginForm @success="redirectAfterLogin('/account')">
      <div class="flex items-center justify-end">
        <div class="text-sm">
          <NuxtLink
            :to="formatLink(`/account/recover`)"
            class="font-medium text-indigo-600 hover:text-indigo-500"
          >
            {{ $t("recoveryPassword.forgotPassword") }}
          </NuxtLink>
        </div>
      </div>

      <template #action>
        <NuxtLink
          :to="formatLink(`/register`)"
          class="w-full flex justify-center py-2 px-4 border border-indigo-600 text-sm font-medium rounded-md text-indigo-600 focus:outline-none focus:ring-2 focus:ring-indigo-500"
        >
          {{ $t("account.signUp") }}
        </NuxtLink>
      </template>
    </AccountLoginForm>
  </div>
</template>
