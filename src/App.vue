<template>
  <div class="container mt-4">
    <div class="row">
      <div class="col-sm-6 mx-auto">
        <form v-on:submit.prevent="registerUser" novalidate>
          <div v-show="step === 1" class="step">
            <div class="form-group">
              <label for="name">Ваше имя</label>
              <!-- v-on:blur - при потере фокуса поля -->
              <!-- $v.formReg.name.$touch() - $v - объект валидатора, $touch() - запустить валидацию -->
              <!-- is-invalid это класс из бутстрапа -->
              <!-- $v.formReg.name.$error - при ошибке (метод встроенный) -->
              <!-- v-model - динамическое связывание, текст из инпута связывается с полем имя -->
              <!-- class="form-control" - тоже из бутстрапа -->
              <input
                v-on:blur="$v.formReg.name.$touch()"
                :class="{ 'is-invalid reqInput': $v.formReg.name.$error }"
                v-model="formReg.name"
                type="text"
                class="form-control"
                id="name"
              />
              <!-- v-if - код сработает, если тру -->
              <!-- !$v.formReg.name.required - воскл знак, отрицание, т.е. сработает если required возвращает не возвращает тру -->
              <!-- class="invalid-feedback" из бутстрапа -->
              <div v-if="!$v.formReg.name.required" class="invalid-feedback">
                {{ msgRequired }}
              </div>
              <!-- !$v.formReg.name.alpha если альфа возвращает не тру. Метод альфа импортировали в коде ниже. Значит, что введено что-то кроме букв -->
              <div v-if="!$v.formReg.name.alpha" class="invalid-feedback">
                {{ msgNotNumber }}
              </div>
            </div>

            <div class="form-group">
              <label for="surname">Ваша фамилия</label>
              <input
                v-on:blur="$v.formReg.surname.$touch()"
                :class="{ 'is-invalid reqInput': $v.formReg.surname.$error }"
                v-model="formReg.surname"
                type="text"
                class="form-control"
                id="surname"
              />
              <div v-if="!$v.formReg.surname.required" class="invalid-feedback">
                {{ msgRequired }}
              </div>
              <div v-if="!$v.formReg.surname.alpha" class="invalid-feedback">
                {{ msgNotNumber }}
              </div>
            </div>

            <div class="form-group">
              <label for="email">Email</label>
              <input
                v-on:blur="$v.formReg.email.$touch()"
                :class="{ 'is-invalid reqInput': $v.formReg.email.$error }"
                v-model="formReg.email"
                type="text"
                class="form-control"
                id="email"
              />
              <div v-if="!$v.formReg.email.required" class="invalid-feedback">
                {{ msgRequired }}
              </div>
              <div v-if="!$v.formReg.email.email" class="invalid-feedback">
                Поле должно быть email адресом
              </div>
            </div>
            <!-- :disabled="disabledButton1" - кнопка заблочена если метод вернет тру. Пока все данные не будут введены верно -->
            <!-- классы из бутстрапа -->
            <button
              v-on:click="nextStep"
              :disabled="disabledButton1"
              type="button"
              class="btn btn-primary"
            >
              Следующий шаг
            </button>
          </div>
          <!-- transition это анимация перехода из вью. Класс взят из документации, стили в стилях внизу -->
          <transition name="slide-fade">
            <!-- step === 2 - если шаг равен 2. метод nextStep внизу -->
            <div v-show="step === 2" class="step">
              <div class="form-group">
                <label for="password">Пароль</label>
                <input
                  v-on:blur="$v.formReg.password.$touch()"
                  :class="{ 'is-invalid reqInput': $v.formReg.password.$error }"
                  v-model="formReg.password"
                  type="password"
                  class="form-control"
                  id="password"
                />
                <div
                  v-if="!$v.formReg.password.required"
                  class="invalid-feedback"
                >
                  {{ msgRequired }}
                </div>
                <!-- метод  minLength импортировали, задали значение 6 -->
                <div
                  v-if="!$v.formReg.password.minLength"
                  class="invalid-feedback"
                >
                  Не меньше 6 символов
                </div>
              </div>

              <div class="form-group">
                <label for="passwordConfirm">Подтверждение пароля</label>
                <input
                  v-on:blur="$v.formReg.passwordConfirm.$touch()"
                  :class="{
                    'is-invalid reqInput': $v.formReg.passwordConfirm.$error,
                  }"
                  v-model="formReg.passwordConfirm"
                  type="password"
                  class="form-control"
                  id="cpasswordConfirm"
                />
                <!-- метод sameAs импортировали -->
                <div
                  v-if="!$v.formReg.passwordConfirm.sameAs"
                  class="invalid-feedback"
                >
                  Пароли не совпадают
                </div>
              </div>
              <!-- метод backStep описали внизу -->
              <button
                v-on:click="backStep"
                type="button"
                class="btn btn-light mr-2"
              >
                Назад
              </button>
              <button
                v-on:click="nextStep"
                :disabled="disabledButton2"
                type="button"
                class="btn btn-primary"
              >
                Следующий шаг
              </button>
            </div>
          </transition>

          <transition name="slide-fade">
            <div v-show="step === 3" class="step">
              <div class="form-group">
                <label for="country">Страна</label>
                <input
                  v-model="formReg.country"
                  v-on:blur="$v.formReg.country.$touch()"
                  :class="{
                    'is-invalid reqInput': $v.formReg.country.$error,
                  }"
                  type="text"
                  class="form-control"
                  id="country"
                />
                <div v-if="!$v.formReg.country.alpha" class="invalid-feedback">
                  {{ msgNotNumber }}
                </div>
              </div>

              <div class="form-group">
                <label for="city">Город</label>
                <input
                  v-model="formReg.city"
                  v-on:blur="$v.formReg.city.$touch()"
                  :class="{
                    'is-invalid reqInput': $v.formReg.city.$error,
                  }"
                  type="text"
                  class="form-control"
                  id="city"
                />
                <div v-if="!$v.formReg.city.alpha" class="invalid-feedback">
                  {{ msgNotNumber }}
                </div>
              </div>
              <!-- mr-2 это класс из бутстрапа -->
              <button
                v-on:click="backStep"
                type="button"
                class="btn btn-light mr-2"
              >
                Назад
              </button>
              <button
                :disabled="disabledRegister"
                type="submit"
                class="btn btn-primary"
              >
                Зарегистрироваться
              </button>
            </div>
          </transition>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
/* это импорт методов из vuelidate. берем только нужные. описаны в документации там же */
import {
  required,
  email,
  helpers,
  minLength,
  sameAs,
} from "vuelidate/lib/validators";
/* уточняем метод helpers значением альфа, передаем русский алфавит и буквы Ёё */
const alpha = helpers.regex("alpha", /^[a-zA-Zа-яёА-яЁ]*$/);

export default {
  data() {
    return {
      step: 1, //начальное значение шага
      msgNotNumber: "Без цифр и спец. символов", //это тексты, лучше сюда выводить все, например, для последующей локализации
      msgRequired: "Поле обязательно для заполнения",
      formReg: {
        //это поля формы, которые будем использовать и проверять. По умолч знач пустые. Еще в конце обнулим.
        name: "",
        surname: "",
        email: "",
        password: "",
        passwordConfirm: "",
        country: "",
        city: "",
      },
    };
  },
  methods: {
    nextStep() {
      //шаг вперед. если больше 3, перестает считать
      if (this.step < 3) {
        this.step++;
      }
    },
    backStep() {
      // то же на назад. менее 1 не считает
      if (this.step > 1) {
        this.step--;
      }
    },
    registerUser() {
      console.log("Регистрация прошла успешно"); //вывод только в консоль. метод привязан в самом верху формы к сабмиту <form v-on:submit.prevent="registerUser" novalidate>

      this.step = 1; //сбрасывает шаг на первый после регистрации.

      for (const input of this.formReg) {
        //сбросить поля
        this.formReg[input] = "";
      }

      this.$v.$reset(); //сбросить валидацию
    },
  },
  computed: {
    //нужно использовать именно вычисляемые свойства, метод почему-то не работает. Не поняла.
    disabledButton1() {
      // блочит кнопку, если хотя бы одно поле инвалидно
      return (
        this.$v.formReg.name.$invalid ||
        this.$v.formReg.surname.$invalid ||
        this.$v.formReg.email.$invalid
      );
    },
    disabledButton2() {
      return (
        this.$v.formReg.password.$invalid ||
        this.$v.formReg.passwordConfirm.$invalid
      );
    },
    disabledRegister() {
      return this.$v.formReg.country.$invalid || this.$v.formReg.city.$invalid;
    },
  },
  validations: {
    //валидаторы, все чем пользуемся и на что смотрим для прохождения валидации
    formReg: {
      name: {
        required,
        alpha,
      },
      surname: {
        required,
        alpha,
      },
      email: {
        required,
        email,
      },
      password: {
        required,
        minLength: minLength(6), //передаем в метод длину 6
      },
      passwordConfirm: {
        sameAs: sameAs("password"), //передаем поле, с которым сравниваем (значение)
      },
      country: {
        alpha,
      },
      city: {
        alpha,
      },
    },
  },
};
</script>

<style>
/* подсветит красным косячное поле */
.reqInput {
  background-color: rgba(255, 177, 177, 0.562);
}
/* это два класса из документации вью по анимации в ссс */
.slide-fade-enter-active {
  transition: all 0.3s ease;
}

.slide-fade-enter {
  transform: translateX(10px);
  opacity: 0;
}
</style>
