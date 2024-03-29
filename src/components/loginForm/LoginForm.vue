<template>
  <transition name="formToSpinner" mode="out-in">
    <base-spinner v-if="isLoading" class="spinner"></base-spinner>
    <form v-else @submit.prevent="submitForm" class="login-form">
      <label for="name" v-if="isSignup" class="input-label">Imie</label>
      <input
        id="name"
        v-if="isSignup"
        type="text"
        v-model.trim="name.val"
        class="input"
        :class="{ 'input--error': !name.isValid }"
      />
      <label for="email" class="input-label">E-mail</label>
      <input
        id="email"
        type="email"
        v-model.trim="email.val"
        class="input"
        :class="{ 'input--error': !email.isValid }"
      />
      <label for="password" class="input-label">Hasło</label>
      <input
        id="password"
        type="password"
        v-model.trim="password.val"
        class="input"
        :class="{ 'input--error': !password.isValid }"
      />
      <div class="form__buttons">
        <base-button-small class="button" mode="black">{{
          submitButtonCaption
        }}</base-button-small>
        <base-button-small
          mode="flat"
          @click.native="switchAuthMode"
          class="button"
          >{{ switchModeButtonCaption }}</base-button-small
        >
      </div>
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
    </form>
  </transition>
</template>

<script>
import BaseSpinner from "../../components/ui/BaseSpinner.vue";
export default {
  components: { BaseSpinner },
  data() {
    return {
      name: { isValid: true, val: "" },
      email: { isValid: true, val: "" },
      password: { isValid: true, val: "" },
      mode: "login",
      isLoading: false,
      errorMessage: null,
    };
  },

  methods: {
    async submitForm() {
      this.errorMessage = null;
      this.name.isValid = true;
      this.password.isValid = true;
      this.email.isValid = true;

      if (this.mode === "signup" && this.name.val === "") {
        this.errorMessage = "Podaj swoje imię.";
        this.name.isValid = false;
        return;
      }
      if (this.email.val === "" || !this.email.val.includes("@")) {
        this.errorMessage = "Podaj prawidłowy adres e-mail.";
        this.email.isValid = false;
        return;
      }
      if (this.password.val.length < 6) {
        this.errorMessage = "Hasło musi posiadać min. 6 znaków.";
        this.password.isValid = false;
        return;
      }

      this.isLoading = true;

      try {
        if (this.mode === "login") {
          const actionPayload = {
            email: this.email.val,
            password: this.password.val,
          };

          await this.$store.dispatch("login", actionPayload);
        } else {
          const actionPayload = {
            email: this.email.val,
            password: this.password.val,
            name: this.name.val,
          };

          await this.$store.dispatch("signup", actionPayload);
        }

        this.clearLoginInputs();
        const redirectUrl = "/" + "gift-list" + "/" + this.userId;
        this.$router.replace(redirectUrl);
      } catch (err) {
        this.errorMessage =
          err.message || "Błąd uwierzytelnienia. Spróbuj ponownie później.";
      }

      this.isLoading = false;
    },

    switchAuthMode(e) {
      e.preventDefault();

      this.name.isValid = true;
      this.password.isValid = true;
      this.email.isValid = true;
      this.errorMessage = null;

      if (this.mode === "login") {
        this.mode = "signup";
      } else {
        this.mode = "login";
      }
    },

    clearLoginInputs() {
      this.name.val = "";
      this.email.val = "";
      this.password.val = "";
    },
  },

  watch: {
    nameValue: function (value) {
      if (value) {
        this.name.isValid = true;
      } else {
        this.name.isValid = false;
      }
    },

    emailValue: function (value) {
      if (value) {
        this.email.isValid = true;
      } else {
        this.email.isValid = false;
      }
    },

    passwordValue: function (value) {
      if (value) {
        this.password.isValid = true;
      } else {
        this.password.isValid = false;
      }
    },
  },

  computed: {
    nameValue() {
      return this.name.val;
    },

    emailValue() {
      return this.email.val;
    },

    passwordValue() {
      return this.password.val;
    },

    switchModeButtonCaption() {
      if (this.mode === "login") {
        return "lub zarejestruj ";
      } else {
        return "lub zaloguj";
      }
    },

    submitButtonCaption() {
      if (this.mode === "login") {
        return "Zaloguj";
      } else {
        return "Zarejestruj";
      }
    },

    userId() {
      return this.$store.getters.userId;
    },

    isSignup() {
      if (this.mode === "signup") {
        return true;
      } else {
        return false;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.login-form {
  display: flex;
  flex-direction: column;
  align-content: center;
  width: 50%;
  min-width: 350px;
  padding: 2rem 4rem 0rem 4rem;
  border: 2px solid var(--v-primary-lighten2);
  border-radius: 10px;
  margin-top: 2rem;

  @media only screen and (max-width: 750px) {
    border: none;
    margin-top: 0;
  }

  .input-label {
    font-size: 1.3rem;
    text-align: center;
    color: var(--v-primary-lighten2);
    margin-top: 2rem;
    margin-bottom: 1rem;
  }

  .input-label:nth-child(1) {
    margin-top: 0rem;
  }

  .input {
    background-color: transparent;
    border: 2px solid var(--v-primary-lighten2);
    border-radius: 8px;
    font-size: 1rem;
    padding-left: 1rem;
    color: var(--v-primary-base);
    height: 40px;

    &:focus {
      border-color: var(--v-teritary-base);
    }
    &[type="submit"] {
      border-radius: 2px;
      background: var(--v-primary-base);
      border: 1px solid var(--v-primary-base);
      color: var(--v-primary-lighten2);
      font-size: 14px;
      font-weight: bold;
      width: 100px;
      padding: 0 16px;
      height: 36px;
    }

    &[type="submit"]:hover {
      box-shadow: 0 1px 1px var(--v-secondary-base);
      background: var(--v-teritary-base);
      border: 1px solid var(--v-primary-lighten1);
      box-shadow: 0 1px 1px var(--v-secondary-base);
      color: var(--v-primary-base);
    }

    &:-webkit-autofill,
    &:-webkit-autofill:hover,
    &:-webkit-autofill:focus {
      box-shadow: 0 0 0 30px var(--v-teritary-base) inset !important;
      color: var(--v-primary-base) !important;
    }
  }

  .input--error {
    border: 2px solid var(--v-quaternary-base);
  }

  .form__buttons {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    height: 120px;

    .button {
      padding: 0.7rem 1rem;
      min-width: 30%;
      align-self: center;
      border-radius: 8px;

      @media only screen and (max-width: 550px) {
        font-size: 0.8rem;
      }
      @media only screen and (max-width: 1200px) {
        font-size: 0.7rem;
      }
    }
  }

  .error-message {
    text-align: center;
    font-size: 0.9rem;
    color: var(--v-primary-base);
    background-color: var(--v-teritary-base);
    padding: 0.5rem 1rem;
    align-self: center;
    border-radius: 10px;
    margin-bottom: 1.5rem !important;
  }

  .spinner {
    align-self: center;
  }
}
.formToSpinner-enter-active,
.formToSpinner-leave-active {
  transition: opacity 0.1s;
}
.formToSpinner-enter, .formToSpinner-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
