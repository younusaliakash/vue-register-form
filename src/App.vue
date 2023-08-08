<script setup>
import { ref, reactive } from "vue";

const isSignInPage = ref(false);
const isFromSubmit = ref(false);
const isUserNotFound = ref(false);
const isLoggedIn = ref(false);

const userData = reactive({
  userName: "",
  password: "",
});

const errors = reactive({
  userData: false,
  password: false,
});

const confirmPassword = ref("");

function validation() {
  let allValid = false;
  if (userData.userName.length === 0) {
    errors.userData = true;
  } else {
    errors.userData = false;
  }
  if (userData.password.length === 0) {
    errors.password = true;
  } else {
    errors.password = false;
  }

  if (errors.userData && errors.password) {
    allValid = false;
  } else {
    allValid = true;
  }

  return allValid;
}

function submitRegister() {
  if (validation()) {
    let exitingData = JSON.parse(localStorage.getItem("userAuth"));
    if (exitingData) {
      exitingData.push(userData);
      localStorage.setItem("userAuth", JSON.stringify(exitingData));
    } else {
      const newData = [userData];
      localStorage.setItem("userAuth", JSON.stringify(newData));
    }

    isFromSubmit.value = true;

    userData.userName = "";
    userData.password = "";
  } else {
    return;
  }
}

function submitSingIn() {
  if (validation()) {
    let existingData = JSON.parse(localStorage.getItem("userAuth"));
    if (existingData) {
      const isUserExist = existingData.filter(
        (user) =>
          user.userName === userData.userName &&
          user.password === userData.password
      );
      if (isUserExist.length) {
        isUserNotFound.value = false;
        isLoggedIn.value = true;
        isFromSubmit.value = true;
      } else {
        isUserNotFound.value = true;
      }

      userData.userName = "";
      userData.password = "";
    }
  } else {
    return;
  }
}
</script>

<template>
  <section class="flex h-screen w-full">
    <div
      class="w-1/2"
      style="
        background-image: url(https://images.unsplash.com/photo-1690555575753-7aa27df96b52?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=774&q=80);
        background-repeat: no-repeat;
        background-size: cover;
      "
    >
      <h1 class="mb-5 text-2xl mt-10 ml-10 text-white">Unicorn</h1>
    </div>
    <div
      v-if="!isFromSubmit"
      class="w-1/2 flex flex-col justify-center items-center bg-gray-200"
    >
      <h2 class="mb-5 text-xl">{{ isSignInPage ? "Sign In" : "Register" }}</h2>
      <div class="w-full max-w-xs">
        <form class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
          <p
            v-if="isUserNotFound"
            class="text-red-500 text-xs italic text-center"
          >
            Username or Password not match or user not exits.
          </p>
          <div class="mb-4">
            <label
              class="block text-gray-700 text-sm font-bold mb-2"
              for="username"
            >
              Username
            </label>
            <input
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              id="username"
              type="text"
              placeholder="Username"
              v-model="userData.userName"
            />
            <p v-if="errors.userData" class="text-red-500 text-xs italic">
              Please provide a username.
            </p>
          </div>
          <div class="mb-6">
            <label
              class="block text-gray-700 text-sm font-bold mb-2"
              for="password"
            >
              Password
            </label>
            <input
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              id="password"
              type="password"
              placeholder="password"
              v-model="userData.password"
            />
            <p v-if="errors.password" class="text-red-500 text-xs italic">
              Please provide a password.
            </p>
          </div>
          <div class="mb-6" v-if="!isSignInPage">
            <label
              class="block text-gray-700 text-sm font-bold mb-2"
              for="confirm_password"
            >
              Confirm Password
            </label>
            <input
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
              id="confirm_password"
              type="password"
              placeholder="Confirm Password"
              v-model="confirmPassword"
            />
            <p
              v-if="!isSignInPage && userData.password !== confirmPassword"
              class="text-red-500 text-xs italic"
            >
              Confirm Password Not Matched!
            </p>
          </div>
          <div class="flex items-center justify-between">
            <button
              v-if="isSignInPage"
              class="bg-orange-600 hover:bg-orange-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
              type="button"
              @click="submitSingIn"
            >
              Sign In
            </button>
            <button
              v-else
              class="bg-orange-600 hover:bg-orange-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
              type="button"
              @click="submitRegister"
            >
              Register
            </button>
            <a
              class="inline-block align-baseline font-bold text-sm text-orange-600 hover:text-orange-800"
              href="#"
              @click="isSignInPage = !isSignInPage"
            >
              Or {{ isSignInPage ? "Register" : "Sign In" }}
            </a>
          </div>
        </form>
        <p class="text-center text-gray-500 text-xs">
          &copy;2020 Acme Corp. All rights reserved.
        </p>
      </div>
    </div>
    <div
      v-else
      class="w-1/2 flex flex-col justify-center items-center bg-gray-200 text-center"
    >
      <h3 v-if="isLoggedIn" class="text-green-500 font-semibold">
        Welcome! You are Logged in!!
      </h3>
      <h3 v-else class="text-green-500 font-bold text-lg">
        Welcome! <br />You are successfully registered!
      </h3>
    </div>
  </section>
</template>
<style scoped></style>
