<template>
  <ion-page class="background">
      <ion-content class="background" >
        <ion-content class="background">
          <ion-row style="padding-top: 15px; align-items: center">
            <ion-col>
              <!-- <ion-img class="ion-float-center" routerLink="/" routerDirection="root"
              src="https://i.postimg.cc/Qd2tjq17/m.png" style="width:50%" /> -->
              <img :src="Logo1" alt="Logo" class="logo" style="width: 20%" />
            </ion-col>
          </ion-row>
          <ion-row>
            <ion-col>
              <ion-text>
                <h2 style="text-align: center; margin-top: 10%; color: aliceblue;">Login</h2>
              </ion-text>
            </ion-col>
          </ion-row>

          <ion-row class="ion-align-items-center ion-justify-content-center" style="color: aliceblue;">
            <div
            style="border: solid 1px; border-radius: 4px; width: 90vw"
            >
              <ion-row style="color: aliceblue;">
                <ion-col class="ion-align-self-center" size="1">
                  <ion-icon :icon="mailOutline" />
                </ion-col>
                <ion-col class="ion-align-items-center">
                  <ion-input
                    v-model="email"
                    name="email"
                    placeholder="Email Address"
                    required
                    autocomplete="email"
                  />
                </ion-col>
              </ion-row>
            </div>
          </ion-row>
          <ion-row
            v-show="emailError"
            class="ion-text-start"
            style="padding-left: 10px; color: aliceblue;"
          >
            <ion-col>
              <ion-text color="danger">
                <span>{{ emailError }}</span>
              </ion-text>
            </ion-col>
          </ion-row>

          <ion-row
            class="ion-align-items-center ion-justify-content-center"
            style="margin-top: 1vh; color: aliceblue;"
          >
            <div style="border: solid 1px; border-radius: 4px; width: 90vw">
              <ion-row>
                <ion-col class="ion-align-self-center" size="1">
                  <ion-icon :icon="lockClosedOutline"></ion-icon>
                </ion-col>
                <ion-col class="ion-align-items-center">
                  <ion-input
                    v-model="password"
                    :type="passwordFieldType"
                    name="password"
                    placeholder="Password"
                    autocomplete="password"
                  >
                  </ion-input>
                </ion-col>

                <ion-col
                  v-show="password"
                  id="eyeIcon"
                  class="ion-align-self-center"
                  size="1"
                >
                  <ion-icon
                    v-if="passwordFieldType !== 'password'"
                    :icon="eye"
                    @click="passwordTongle"
                  />
                  <ion-icon v-else :icon="eyeOff" @click="passwordTongle" />
                </ion-col>
              </ion-row>
            </div>
          </ion-row>
          <ion-row
            v-show="passwordError"
            class="ion-text-start"
            style="padding-left: 10px"
          >
            <ion-col>
              <ion-text color="danger">
                <span>{{ passwordError }}</span>
              </ion-text>
            </ion-col>
          </ion-row>
          <div
            style="
              display: flex;
              flex-direction: row;
              justify-content: center;
              align-items: center;
              right: -0.4px;
              border-radius: 4px;
            "
          >
            <ion-row class="ion-align-items-center ion-justify-content-center">
              <ion-col style="">
                <ion-button
                  :disabled="is_btn_loading"
                  expand="full"
                  style="height: 50px"
                  @click="submit"
                >
                  <ion-spinner
                    name="circles"
                    :hidden="!is_btn_loading"
                  ></ion-spinner>
                  LOGIN
                </ion-button>
              </ion-col>
            </ion-row>
          </div>
          <ion-row>
            <ion-col>
              <div class="ion-text-center ion-margin-top">
                <span>
                  <p style="color: aliceblue;" @click="() => router.push('/register')">
                    Don't have an account?
                  </p>
                </span>
              </div>
            </ion-col>
          </ion-row>
        </ion-content>
      </ion-content>
    
  </ion-page>
</template>

<script>
import {
  IonButton,
  IonCard,
  IonCardHeader,
  IonCol,
  IonContent,
  IonFooter,
  IonGrid,
  IonHeader,
  IonIcon,
  IonImg,
  IonInput,
  IonItem,
  IonLabel,
  IonPage,
  IonRow,
  IonSpinner,
  IonText,
  IonThumbnail,
  IonTitle,
  IonToolbar,
} from "@ionic/vue";
import { defineComponent } from "vue";
import { useRouter } from "vue-router";
import { eye, eyeOff, lockClosedOutline, mailOutline } from "ionicons/icons";
import { defineRule, useField, useForm } from "vee-validate";
import authAPI from "../../apis/modules/auth_api";
// import {toast} from "@/common/toast";
import Logo1 from "@/assets/images/logo1.png"; // Adjust the path to your image

/*dis*/
export default defineComponent({
  name: "Login",
  components: {
    IonSpinner,
    IonGrid,
    IonText,
    IonIcon,
    IonThumbnail,
    IonImg,
    IonContent,
    IonHeader,
    IonTitle,
    IonCard,
    IonCardHeader,
    IonToolbar,
    IonFooter,
    IonButton,
    IonItem,
    IonInput,
    IonLabel,
    IonCol,
    IonRow,
    IonPage,
  },
  data() {
    return {
      is_btn_loading: false,
      v: "",
      errors: "",
      email: "",
      password: "",
      passwordFieldType: "password",
      Logo1: Logo1,
    };
  },
  setup() {
    // validation rules

    // require
    defineRule("requiredEmail", (value) => {
      if (!value || !value.length) {
        return "The Email field is required";
      }
      return true;
    });

    defineRule("requiredPassword", (value) => {
      if (!value || !value.length) {
        return "The Password field is required";
      }
      return true;
    });

    // checking valid email
    defineRule("email", (email) => {
      if (
        !/^[_a-z0-9-]+(\.[_a-z0-9-]+)*@[a-z0-9-]+(\.[a-z0-9-]+)*(\.[a-z]{2,3})$/.test(
          email
        )
      ) {
        return "Please enter valid email address";
      }
      return true;
    });

    // checking valid email
    defineRule("password", (password) => {
      if (
        !/(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%&*()]).{8,}/.test(password)
      ) {
        return "Your password must contain at least one uppercase, one lowercase, one special character and one digit";
      }
      return true;
    });

    function validation() {
      return theForm.validate();
    }

    const schema = {
      email: "requiredEmail|email",
      password: "requiredPassword",
    };

    // Create a form context with the validation schema
    const theForm = useForm({
      validationSchema: schema,
    });

    // No need to define rules for fields
    const { value: email, errorMessage: emailError } = useField("email");
    const { value: password, errorMessage: passwordError } =
      useField("password");

    const router = useRouter();
    return {
      validation,
      emailError,
      passwordError,
      email,
      password,
      router,
      lockClosedOutline,
      mailOutline,
      eye,
      eyeOff,
    };
  },
  computed: {
    passwordToggleIcon() {
      return this.passwordFieldType === "password" ? "eyeOff" : "eye";
    },
  },
  methods: {
    async submit() {
      this.v = await this.validation();
      if (this.v.valid) {
        await this.Login();
      }
    },
    async Login() {
      try {
        this.is_btn_loading = true;
        let payload = {
          email: this.email,
          password: this.password,
        };
        let respond = (await authAPI.login(payload)).data;
        localStorage.setItem("user", JSON.stringify(respond));
        this.successToast("You are logged in successfully");
        window.location = "/dash_board";
      } catch (e) {
        console.log(e.message);
        await this.dangerToast("Your email or password is incorrect");
      }
      this.is_btn_loading = false;
    },

    passwordTongle() {
      this.passwordFieldType =
        this.passwordFieldType === "password" ? "text" : "password";
    },
  },
});
</script>

<style scoped>
.logo {
  /* Add your logo styles here */
  display: block; /* Ensure the image is a block element */
  margin: 0 auto; /* Center the image horizontally */
  border: 2px solid #ffffff; /* Add a border (adjust the color and width as needed) */
  border-radius: 50%; /* Apply border-radius for a circular shape (adjust as needed) */
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5); /* Add a shadow (adjust as needed) */
}

ion-content.background{
    --background: url(../../assets/images/background.jpg) 0 0/100% 100% no-repeat;
    color: #ffffff;
}

.background-container {
  background-image: url('../../assets/images/background.jpg');
  background-size: cover; /* Change to 'cover' */
  background-position: center;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}

.login-wrap {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 20px;
  border-radius: 10px;
  position: relative; /* Add this line */
  z-index: 2; /* Add this line */
}

.login-wrap {
  /* ... */
  padding: 0; /* Remove padding */
  margin: 0; /* Remove margin */
  /* ... */
}

.auth-form ion-row {
  height: 100%;
  justify-content: center;
}

.already {
  display: block;
  text-align: center;
  padding-bottom: 10px;
}

ion-icon {
  font-size: 20px;
  padding: 5px;
}

.center {
  margin-left: 2vh;
  margin-right: 4vh;
}

ion-button {
  --background: #00c49a;
  height: 5vh;
  width: 90vw;
  margin: 15px;
}

ion-item {
  --highlight-color-focused: none;
  --focused: none;
}

#eyeIcon {
  margin-right: 10px;
  margin-top: 5px;
}
</style>
