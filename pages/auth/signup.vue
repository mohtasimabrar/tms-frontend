<template>
  <div class=" flex flex-column flex-wrap justify-content-center align-content-center align-items-center"
    style="height: 100%; min-height: 98vh">
    <Toast />
    <img src="~/assets/Logo_big.png" alt="" class="max-w-18rem mb-5">
    <div class="card flex flex-column p-7 bg-white border-round shadow-2 lg-w-30 sm-w-90" style="height: 30%;">
      <GlobalInputText type="text" v-model="firstName" placeholder="First Name" class="w-full mb-2 border-round" />
      <GlobalInputText type="text" v-model="lastName" placeholder="Last Name" class="w-full mb-2 border-round" />
      <GlobalInputText type="text" v-model="userName" placeholder="User Name" class="w-full mb-2 border-round" />
      <GlobalInputText type="text" v-model="phoneNumber" placeholder="Phone Number" class="w-full mb-2 border-round" />
      <GlobalInputText type="text" v-model="email" placeholder="Email" class="w-full mb-2 border-round" />
      <GlobalInputText type="password" v-model="password" placeholder="Password" class="w-full mb-4 border-round" />
      <!-- Loader and Sign Up button -->
      <div v-if="loading" class="loader">Loading...</div>
      <GlobalButton v-else @buttonTapped="handleButtonTap" title="Sign Up" :disabled="loading" />
      <div class="w-full text-center pt-4">Already a member? <NuxtLink class="signupLink font-bold" to="/auth/signin">
          Sign In</NuxtLink>
      </div>
      <div class="w-full text-center pt-4">
        <NuxtLink class="signupLink font-bold" to="/">Back to Home</NuxtLink>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { isAuthenticatedState } from '~/composables/state';

definePageMeta({
  layout: 'auth'
})
const email = ref(null);
const password = ref(null);
const phoneNumber = ref(null);
const userName = ref(null);
const firstName = ref(null);
const lastName = ref(null);
const toast = useToast()
const loading = ref(false); // Loading state
const handleButtonTap = () => {
  onSubmit()
};

const onSubmit = async () => {
  if (email.value === null || email.value === "" ||
    password.value === null || password.value === "" ||
    phoneNumber.value === null || phoneNumber.value === "" ||
    userName.value === null || userName.value === "" ||
    firstName.value === null || firstName.value === "" ||
    lastName.value === null || lastName.value === "") {
    toast.add({ severity: 'error', summary: 'Error', detail: "Please fill up all the fields", life: 3000 });
  } else {
    loading.value = true
    useFetch('https://api.countersbd.com/api/v1/user/signup', {
      method: "POST",
      body: {
        firstName: firstName,
        lastName: lastName,
        email: email.value,
        password: password.value,
        phoneNumber: phoneNumber.value,
        userName: userName.value,
        userRole: 1
      }
    }).then(res => {
      const data = res.data.value
      const error = res.error.value
      if (error) {
        // dealing error
        toast.add({ severity: 'error', summary: 'Opps!', detail: error.data.message, life: 3000 });
        console.log(error);
        
        loading.value = false
      } else {
        console.log(data)
        if (data.responseCode !== null && data.responseCode === 200) {
          const userToken = useCookie('userToken')
          userToken.value = data.data.token
          const isAuthenticated = isAuthenticatedState()
          isAuthenticated.value = true
          loading.value = false
          navigateTo("/")
        } else {
          loading.value = false
          console.log(data);
          
          toast.add({ severity: 'error', summary: 'Opps!', detail: data.message, life: 3000 });
        }
      }
    }, error => {
      loading.value = false
      console.log(error);
      
      toast.add({ severity: 'error', summary: 'Opps!', detail: error.data.message, life: 3000 });
    })
  }
}


</script>

<style lang="scss" scoped>
.signupLink {
  text-decoration-color: $theme-yellow;
}

a {
  color: $theme-yellow;
}

/* Unvisited link  */
a:visited {
  color: $theme-yellow;
}

/* Visited link    */
a:hover {
  color: $theme-yellow;
}

/* Mouse over link */
a:active {
  color: $theme-yellow;
}

/* Loader styles */
.loader {
  font-size: 1.2rem;
  text-align: center;
  color: $theme-yellow;
}

/* Selected link   */
</style>