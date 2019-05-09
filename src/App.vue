<template>
  <div class="container">
    <h3 class="text-center mt-3">Vuelidate ile Form Kontrolü</h3>
    <div class="d-flex justify-content-center align-content-center flex-row">
      <div class="card p-4 mt-3 mr-4  shadow">
        <form style="width: 350px" @submit.prevent="onSubmit">
          <div class="form-group">
            <label>E-posta Adresiniz</label>
            <input 
            @blur="$v.email.$touch()"
            v-model="email" 
            type="email" 
            class="form-control"
            :class="{'is-invalid' : $v.email.$error}" 
            placeholder="E-posta adresini giriniz">
            <small v-if="!$v.email.required" class="form-text text-danger">Bu alan zorunludur..</small>
            <small v-if="!$v.email.email" class="form-text text-danger">Lutfen gecerli eposta addressi giriniz..</small>
          </div>
          <div class="form-group">
            <label>Şifre</label>
            <input v-model="$v.password.$model" type="password" class="form-control" placeholder="Şifrenizi giriniz">
            <small v-if="!$v.password.required" class="form-text text-danger">Bu alan zorunludur..</small>
            <small v-if="!$v.password.numeric" class="form-text text-danger">Lutfen sifreniz rakamlardan olussun..</small>
            <small v-if="!$v.password.maxLength" class="form-text text-danger">Lutfen sifreniz en cok {{$v.password.$params.maxLength.max }} karakterden olussun..</small>
            <small v-if="!$v.password.minLength" class="form-text text-danger">Lutfen sifreniz en az {{$v.password.$params.minLength.min }} karakterden olussun..</small>
          </div>
          <div class="form-group">
            <label>Şifre Tekrar</label>
            <input v-model="$v.repassword.$model" type="password" class="form-control" placeholder="Şifrenizi tekrar giriniz">
            <small v-if="!$v.repassword.required" class="form-text text-danger">Bu alan zorunludur..</small>
            <small v-if="!$v.repassword.numeric" class="form-text text-danger">Lutfen sifreniz rakamlardan olussun..</small>
            <small v-if="!$v.repassword.maxLength" class="form-text text-danger">Lutfen sifreniz en cok {{$v.repassword.$params.maxLength.max }} karakterden olussun..</small>
            <small v-if="!$v.repassword.minLength" class="form-text text-danger">Lutfen sifreniz en az {{$v.repassword.$params.minLength.min }} karakterden olussun..</small>
            <small v-if="!$v.repassword.sameAs" class="form-text text-danger">Girdiyiniz sifreler bir birleri ile uyusmuyor..</small>
          </div>
          <div class="form-group">
            <label>Yasiniz</label>
            <input v-model="$v.age.$model" type="text" class="form-control" placeholder="Yasinizi giriniz...">
            <small v-if="!$v.age.numeric" class="form-text text-danger">Sadece rakam giriniz..</small>
            <small v-if="!$v.age.required" class="form-text text-danger">Bu alan zorunludur..</small>
            <small v-if="!$v.age.between" class="form-text text-danger">
              Yasiniz sisteme kayit ola bilmeniz icin {{ $v.age.$params.between.min }}
              ile {{ $v.age.$params.between.max }} arasinda olmalidir..</small>

          </div>
          <div class="form-group">
            <label>Kayıt olmak istediğiniz kategori</label>
            <select v-model="$v.selectedCategory.$model" class="form-control">
              <option v-for="category in categories" :value="category">{{ category }}</option>
            </select>
            <small v-if="!$v.selectedCategory.checked" class="form-text text-danger">Sadece yazilim kategorisine aid kayt olustura bilirsiniz..</small>
          </div>

          <a @click="newHobby" class="text-white btn btn-secondary rounded-0 btn-sm">İlgi Alanı Ekle</a>
          <small v-if="!$v.hobbies.minLength" class="form-text text-danger">Ilgi alanlariniz minimum {{$v.hobbies.$params.minLength.min }} olmalidir</small>
          <small v-if="!$v.hobbies.required" class="form-text text-danger">Bu alan zorunludur..</small>
           
          <ul class="list-group mt-3 mb-3 border-0">            
            <li v-for="(hobby,index) in hobbies" class="list-group-item d-flex pl-2">
              <input 
              type="text" 
              class="form-control mr-2" 
              @blur="$v.hobbies.$each[index].value.$touch()"
              :class="{'is-invalid' : $v.hobbies.$each[index].$error}"
              v-model="hobby.value">
              <button class="btn btn-sm btn-danger rounded-0" @click="hobbies.splice(index, 1)">Sil</button>
            </li>
          </ul>
         
          <button :disabled="$v.$invalid" class="btn btn-outline-secondary rounded-0">Kaydet</button>
        </form>
      </div>

    </div>
  </div>
</template>
<script>
  import { required, email, numeric, minLength, maxLength, sameAs, between } from "vuelidate/lib/validators"
  export default {
    data() {
      return {
        email : null,
        age : null,
        password : null,
        repassword : null,
        selectedCategory : "Yazılım",
        categories : ["Yazılım", "Donanım", "Cloud", "Sunucular", "Unix", "Linux", "Mac OS", "Windows"],
        hobbies: []
      }
    },
    validations : {
      email : {
        required,
        email,
        // uniq : value => {
        //   return value !== "r-firdovsi@hotmail.com" ? true : false
        // }
        uniq : value => {
          // value !== "r-firdovsi@hotmail.com" ? true : false
          return new Promise((resolve,reject) => {
            setTimeout(() => {
              resolve(value !== "r-firdovsi@hotmail.com");
            }, 1000)
            
          })
        }
      },
      password : {
        required,
        numeric,
        minLength : minLength(6),
        maxLength : maxLength(8)
      },
      repassword : {
        required,
        numeric,
        minLength : minLength(6),
        maxLength : maxLength(8),
        sameAs : sameAs('password')
        // sameAs : sameAs(vm => {
        //   return vm.password + "88";
        // })
      },
      age : {
        required,
        numeric,
        between : between(18,68)
      },
      selectedCategory : {
        checked(val, vm) {
          return vm.selectedCategory === "Yazılım" ? true : false
        }
      },
      hobbies : {
        required,
        minLength : minLength(2),
        $each : {
          value : {
            required,
            minLength : minLength(6)
          }
        }
      }
    },
    methods: {
      onSubmit(){
        let form = {
          email : this.email,
          password : this.password,
          category : this.category,
          hobbies : this.hobbies,
        }
        console.log(form)
      },
      newHobby(){
        let hobby = {
          id: Math.random() * Math.random() * 1000,
          value: ''
        }
        this.hobbies.push(hobby)
      }
    }
  }
</script>
