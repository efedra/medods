<script setup>
import '../assets/validate.css'
import {reactive, ref, watch, onMounted} from 'vue'
import {useVuelidate} from '@vuelidate/core'
import {required, minLength, maxLength, numeric} from '@vuelidate/validators'
import {helpers} from '@vuelidate/validators'

const form = ref({
  surname: "",
  name: "",
  secondName: "",
  dateBirth: "",
  phone: "",
  sex: false,
  typeClient: new Set(["Клиент"]),
  doctor: "Лечащий врач",
  sms: false,

  index: "",
  city: "",
  region: "",
  country: "",
  street: "",
  house: "",

  typeDocument: "Тип Документа",
  seria: "",
  number: "",
  whoGive: "",
  dateGive: ""
})

const openMultiselect = ref(false)
const openSelect = ref(false)
const openSelectPassport = ref(false)

function checkTypeDocument(value) {
  return value !== "Тип Документа"
}

const phoneValidator = (value) => {
  const phoneRegex = /^[7]{1}[0-9]{10}$/;
  return phoneRegex.test(value);
};
const birthdayValidator = (value) => {
  const birthdayRegex = /^(0[1-9]|[12][0-9]|3[01])\.(0[1-9]|1[0-2])\.\d{4}$/;
  return birthdayRegex.test(value);
};
const rules = {
  surname: {required, minLength: minLength(2)},
  name: {required},
  secondName: {required},
  dateBirth: {required, birthdayValidator},
  phone: {required, numeric, phoneValidator, minLength: minLength(11), maxLength: maxLength(11)},
  typeClient: {
    required: helpers.withParams({type: 'required'}, (value) => {
      return !value.has("Клиент");
    })
  },

  city: {required},

  typeDocument: {required, checkTypeDocument},
  dateGive: {required, birthdayValidator}
}

const v$ = useVuelidate(rules, form)
const sendOk = ref(false)


function sendForm(v) {
  if (v.$invalid) {
    v.$touch();
    this.sendOk = false
  } else {
    this.sendOk = true
  }
}


function clearForm(v) {
  v.$reset()
  this.sendOk = false
  this.form.surname = ""
  this.form.name = ""
  this.form.secondNam = ""
  this.form.dateBirth = ""
  this.form.phone = ""
  this.form.sex = false, this.form.typeClient = new Set(["Клиент"])
  this.form.doctor = "Лечащий врач",
      this.form.sms = false

  this.form.index = "", this.form.city = "",
      this.form.region = "",
      this.form.country = "",
      this.form.street = "",
      this.form.house = "",

      this.form.typeDocument = "Тип Документа",
      this.form.seria = "",
      this.form.number = "",
      this.form.whoGive = "",
      this.form.dateGive = ""
}

function pushClient(str) {
  if (!form.value.typeClient.has(str)) {
    form.value.typeClient.delete("Клиент")
    form.value.typeClient.add(str)
  }
}

function deleteClient(str, event) {
  event.stopPropagation();
  form.value.typeClient.delete(str)
  if (form.value.typeClient.size === 0) {
    form.value.typeClient.add("Клиент")
  }
}


</script>
<template>
  <div class="container main main_margin">
    <h2 class="main-header">Валидация формы</h2>
    <form>
      <div class="validate-subtext">*Поле обязательное для заполнения.</div>
      <div class="validate-person validate-block">
        <p class="validate-subtext">Информация о вас:</p>
        <div class="validate-person__grid">
          <div class="validate-person__elem_1">
            <input v-model="form.surname" type="text" placeholder="Фамилия*">
            <div :class="{error: v$.surname.$error}" v-if="v$.surname.$error">Поле должно быть больше 2х символов</div>
          </div>
          <div class="validate-person__elem_2">
            <input v-model="form.name" class="validate-person__elem_2" type="text" placeholder="Имя*">
            <div :class="{error: v$.name.$error}" v-if="v$.name.$error">Поле должно быть не пустым</div>
          </div>
          <div class="validate-person__elem_3">
            <input v-model="form.secondName" class="validate-person__elem_3" type="text" placeholder="Отчество*">
            <div :class="{error: v$.secondName.$error}" v-if="v$.secondName.$error">Поле должно быть не пустым</div>
          </div>
          <div class="validate-person__elem_4">
            <input v-model="form.dateBirth" class="validate-person__elem_4" type="text" placeholder="Дата рождения*">
            <div :class="{error: v$.dateBirth.$error}" v-if="v$.dateBirth.$error">Поле должно быть не пустым и иметь
              формат ДД.ММ.ГГГГ
            </div>
          </div>
          <div class="validate-person__elem_5">
            <input v-model="form.phone" class="validate-person__elem_5" type="text" placeholder="Номер телефона*">
            <div :class="{error: v$.phone.$error}" v-if="v$.phone.$error">Поле должно быть не пустым и иметь формат
              7**********
            </div>
          </div>
          <div class="validate-person__elem_6">
            <span>Пол: </span>
            <label class="switch">
              <input v-model="form.sex" type="checkbox">
              <span class="slider round"></span>
            </label>
          </div>
          <div class="validate-person__elem_7 multiselect">
            <div @click="openMultiselect = !openMultiselect" class="multiselect__head">
              <span @click="deleteClient(el,$event)"
                    class="multiselect__select-label"
                    :class="{ 'multiselect__select-label_client': el === 'Клиент' }"
                    v-for="el in form.typeClient">{{ el }}</span>
            </div>
            <div :class="{error: v$.typeClient.$error}" v-if="v$.typeClient.$error">Выберите элемент</div>
            <div v-if="openMultiselect" class="multiselect__body">
              <div @click="pushClient('VIP')">VIP</div>
              <div @click="pushClient('ОМС')">ОМС</div>
              <div @click="pushClient('Проблемные')">Проблемные</div>
            </div>
          </div>
          <div ref="select" class="validate-person__elem_8 select">
            <div @click="openSelect = !openSelect" class="select__head">{{ form.doctor }}</div>
            <div v-if="openSelect" class="select__body">
              <div @click="form.doctor='Иванов'">Иванов</div>
              <div @click="form.doctor='Захаров'">Захаров</div>
              <div @click="form.doctor='Чернышева'">Чернышева</div>
            </div>
          </div>
          <label class="validate-person__elem_9">
            <input v-model="form.sms" type="checkbox" name="sendSMS" value="1">
            <div>Не отправлять СМС</div>
          </label>
        </div>
      </div>
      <div class="validate-adress validate-block">
        <p class="validate-subtext">Адрес проживания:</p>
        <div class="validate-adress__grid">
          <input v-model="form.index" type="text" placeholder="Индекс">
          <input v-model="form.country" type="text" placeholder="Страна">
          <input v-model="form.region" type="text" placeholder="Область">
          <div>
            <input v-model="form.city" type="text" placeholder="Город*">
            <div :class="{error: v$.city.$error}" v-if="v$.city.$error">Поле должно быть не пустым</div>
          </div>

          <input v-model="form.street" type="text" placeholder="Улица">
          <input v-model="form.house" type="text" placeholder="Дом">
        </div>
      </div>
      <div class="validate-pasport validate-block validate-pasport_margin">
        <p class="validate-subtext">Паспортные данные:</p>
        <div class="validate-pasport__grid">
          <div class="validate-pasport__grid_elem-1 select">
            <div>
              <div @click="openSelectPassport = !openSelectPassport" class="select__head">{{ form.typeDocument }}</div>
              <div :class="{error: v$.typeDocument.$error}" v-if="v$.typeDocument.$error">Выберите элемент</div>
            </div>
            <div v-if="openSelectPassport" class="select__body">
              <div @click="form.typeDocument='Паспорт'"> Паспорт</div>
              <div @click="form.typeDocument='Свидетельство о рождении'">Свидетельство о рождении</div>
              <div @click="form.typeDocument='Вод. удостоверение'">Вод. удостоверение</div>
            </div>
          </div>
          <input v-model="form.serie" class="validate-pasport__grid_elem-2" type="text" placeholder="Серия">
          <input v-model="form.number" class="validate-pasport__grid_elem-3" type="text" placeholder="Номер">
          <input v-model="form.whoGive" class="validate-pasport__grid_elem-4" type="text" placeholder="Кем выдан">
          <div class="validate-pasport__grid_elem-5">
            <input v-model="form.dateGive" type="text" placeholder="Дата выдачи*">
            <div :class="{error: v$.dateGive.$error}" v-if="v$.dateGive.$error">Поле должно быть не пустым и иметь
              формат ДД.ММ.ГГГГ
            </div>
          </div>

        </div>
      </div>
      <div v-if="sendOk && v$.$error===false" class="ok">Данные прошли валидацию
      </div>
      <div class="form-buttons">
        <div @click="sendForm(v$)" class="form-buttons_button">Отправить</div>
        <div @click="clearForm(v$)" class="form-buttons_button form-buttons_button_clear">Очистить</div>
      </div>
    </form>
  </div>

</template>


<style scoped>

</style>