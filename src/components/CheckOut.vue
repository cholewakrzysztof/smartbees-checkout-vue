<script>
import axios from "axios";

export default {
  data() {
    return {
        recaptchaDisabled : true,
        another_location : false,
        create_account : false,
        newsletter : false,
        statute : false,
        deliver_type : "",
        payment_type : "",
        text : "",
        address : "",
        another_address : "",
        password :"",
        repeat_password : "",
        login:"",

        name : "",
        surname : "",
        country : "Polska",
        street : "",
        house_number : "",
        zip_code : "",
        city : "",
        phone: "",

        another_country : "Polska",
        another_street : "",
        another_house_number : "",
        another_zip_code : "",
        another_city : "",

        show_login_form : false,
        show_discount_form : false,
        show_order_popup : false,
        order_title:"",

        user_discount : "",
        discount_result : "",
    
        base : 115.00,
        discount : 0,
        deliver : 0,
        total : 0,

        order:{
            name : "",
            surname : "",
            country : "",
            street : "",
            house_number : "",
            zip_code : "",
            city : "",
            phone: "",
            deliver_type : "",
            payment_type : "",
            discount_code : "",
            total : 0,
            comment : ""
        },

        error_list:{
            name : false,
            surname : false,
            country : false,
            street : false,
            house_number : false,
            zip_code : false,
            city : false,
            another_country : false,
            another_street : false,
            another_house_number : false,
            another_zip_code : false,
            another_city : false,
            phone: false,
            comment : false,
            password:false,
            login:false,
        },

        errors : [],
        generated_code : "",
    }
  },
  mounted(){
      let recaptchaScript = document.createElement('script');
      recaptchaScript.setAttribute('src', 'https://www.google.com/recaptcha/api.js');
      document.head.appendChild(recaptchaScript);
      window.myRecaptchaMethod = this.myRecaptchaMethod;
  },
  methods: {
      notImplemented(){
          alert("Function not implemented");
      },
      clearPayment(){
          this.payment_type = "";
      },
      countDeliver(){
        if (this.deliver_type=="Paczkomat")
            this.deliver = 10.99;
        if (this.deliver_type=="Kurier")
            this.deliver = 18;
        if (this.deliver_type=="Kurier za pobraniem")
            this.deliver = 22;
      },
      countTotal(){
          this.total = (this.base*((100-this.discount)/100))+this.deliver;
      },
      myRecaptchaMethod: function(response) {
        this.recaptchaDisabled=false;
      },
      decodeAddress(){
        this.another_street = "";
        this.another_house_number = "";
        this.street = "";
        this.house_number = "";

        let params = this.another_address.split(",");
            if(params.length==2){
                this.another_street = params[0];
                this.another_house_number = params[1];
            }
        params = this.address.split(",");
            if(params.length==2){
                this.street = params[0];
                this.house_number = params[1];
            }
      },
           setPhone(){
         this.phone = this.phone.replace(" ","");
     },
      trySendOrder(){
          if(this.checkForm()){
              this.sendOrder();
              this.show_order_popup = true;

              setTimeout(()=>{
                  this.show_order_popup = false;
              },10000);
          }
      },
      tryGetDiscount(){
          if(this.user_discount.length==0){
              this.discount_result = "Wprowadź kod rabatowy";
              return;
          }
          let url = 'http://localhost/discount_check.php?q='+this.user_discount;
          axios.get(url).then((response)=>{
              this.setDiscountResult(response.data);
          });
      },
      setDiscountResult(response){
          this.discount = 0;
          if(response=="Wrong code"){
              this.discount_result = "Nieprawidłowy kod";
              return;
          }
          if(response=="Not active code"){
              this.discount_result = "Kod jest nieaktywny";
              return;
          }
          if(!isNaN(Number(response))){
              this.discount = Number(response);
              this.discount_result = "Kod rabatowy wynosi: "+this.discount+"%";
          }else{
              this.discount_result = "Wystąpił nieznany błąd";
          }
      },
      clearErrorList(){
            this.error_list.name = false;
            this.error_list.surname = false;
            this.error_list.country = false;
            this.error_list.street = false;
            this.error_list.house_number = false;
            this.error_list.zip_code = false;
            this.error_list.city = false;
            this.error_list.another_country = false;
            this.error_list.another_street = false;
            this.error_list.another_house_number = false;
            this.error_list.another_zip_code = false;
            this.error_list.another_city = false;
            this.error_list.phone= false;
            this.error_list.comment = false;
            this.error_list.password= false;
            this.error_list.login= false;
      },
      sendOrder(){
        this.buildOrder();
        this.generated_code = "";
        this.order_title = "";
        let data = JSON.stringify(this.order);

        console.log(data);

        let url = 'http://localhost/send_order.php?data='+data;
        axios.get(url).then((response)=>{
              if(response.data.length>30){
                  this.order_title = "Coś poszło nie tak";
                  this.generated_code = response.data;
              }else{
                  this.order_title = "Dziękujemy za zakupy";
                  this.generated_code= "Oto numer zamówienia: "+response.data;
              }
        });
      },
      buildOrder(){
            this.order.name = this.name;
            this.order.surname = this.surname;
            if(this.another_location){
                this.order.country = this.another_country;
                this.order.street = this.another_street;
                this.order.house_number = this.another_house_number;
                this.order.zip_code = this.another_zip_code;
                this.order.city = this.another_city;
            }else{
                this.order.country = this.country;
                this.order.street = this.street;
                this.order.house_number = this.house_number;
                this.order.zip_code = this.zip_code;
                this.order.city = this.city;
            }
            this.order.deliver_type = this.deliver_type;
            this.order.payment_type = this.payment_type;
            this.order.total = this.total*100;
            this.order.phone = this.phone;
            this.order.discount_code = this.user_discount;
            this.order.comment = this.text;
      },
      checkForm(){
            let isOk = true;

            this.clearErrorList();

            this.errors = [];
            if(this.create_account){
                if(this.login.length<5){
                    isOk = false;
                    this.errors.push("Login musi mieć co najmniej 5 znaków"); 
                    this.error_list.login=true; 
                }
                if(this.password!=this.repeat_password){
                    isOk = false;
                    this.errors.push("Hasła muszą być takie same");   
                     this.error_list.password=true;
                }else{
                    if(this.password.length<8){
                        isOk = false;
                        this.errors.push("Hasło musi mieć co najmniej 8 znaków"); 
                         this.error_list.password=true;
                    }
                }
            }
            
            if(this.recaptchaDisabled){
                isOk = false;
                this.errors.push("Zaakceptuj reCaptche");   
            }
            if(this.deliver_type==""){
                isOk = false;
                this.errors.push("Wybierz rodzaj dostawy");
            }
            if(this.payment_type==""){
                isOk = false;
                this.errors.push("Wybierz metodę płatności");
            }
            if(!this.statute){
                isOk = false;
                this.errors.push("Musisz zaakceptować regulamin");
            }
            if(this.another_location){
                if(this.another_country=="")
                    {isOk =false;this.errors.push("Wybierz kraj dla dostawy");this.error_list.another_country=true;}
                if(this.another_street==""||this.another_house_number=="")
                    {isOk =false;this.errors.push("Podaj ulicę oraz numer domu dla dostawy, według wzorca: ulica,numer domu");this.error_list.another_street=true;}
                if(this.another_zip_code=="")
                    {isOk =false;this.errors.push("Podaj kod pocztowy dla dostawy");this.error_list.another_zip_code=true;}
                else{
                    let params = this.another_zip_code.split("-");
                    if(params.length!=2){
                        isOk=false;
                        this.errors.push("Nieprawidłowy kod pocztowy dla dostawy");this.error_list.another_zip_code=true;
                    }
                    else if(params[0].length!=2||params[1].length!=3){
                        isOk=false;
                        this.errors.push("Nieprawidłowy kod pocztowy dla dostawy");this.error_list.another_zip_code=true;
                    }
                }
                if(this.another_city=="")
                    {isOk =false;this.errors.push("Podaj miasto dla dostawy");this.error_list.another_city=true;}
                else if(this.another_city.length>30){
                    isOk=false; this.errors.push("Za długa nazwa miasta dla dostawy");this.error_list.another_city=true;
                }
            }

                if(this.country=="")
                    {isOk =false;this.errors.push("Wybierz kraj");this.error_list.country=true;}
                if(this.street==""||this.house_number=="")
                    {isOk =false;this.errors.push("Podaj ulicę oraz numer domu dla dostawy, według wzorca: ulica,numer domu");this.error_list.street=true;}
                if(this.zip_code=="")
                    {isOk =false;this.errors.push("Podaj kod pocztowy");this.error_list.zip_code=true;
                }else{
                    let params = this.zip_code.split("-");
                    if(params.length!=2){
                        isOk=false;
                        this.errors.push("Nieprawidłowy kod pocztowy");this.error_list.zip_code=true;
                    }
                    else if(params[0].length!=2||params[1].length!=3){
                        isOk=false;this.errors.push("Nieprawidłowy kod pocztowy");this.error_list.zip_code=true;
                    }
                }
                if(this.city=="")
                    {isOk =false;this.errors.push("Podaj miasto");this.error_list.city=true;
                }else if(this.city.length>30){
                    isOk=false; this.errors.push("Za długa nazwa miasta");this.error_list.city=true;
                }
            if(this.name==""){
                isOk=false; this.errors.push("Podaj imię");this.error_list.name=true;
            }else if(this.name.length>40){
                isOk=false; this.errors.push("Za długie imię");this.error_list.name=true;
            }
            if(this.surname==""){
                isOk=false; this.errors.push("Podaj nazwisko");this.error_list.surname=true;
            }else if(this.surname.length>40){
                isOk=false; this.errors.push("Za długie nazwisko");this.error_list.surname=true;
            }
            if(this.phone==""){
                isOk=false; this.errors.push("Podaj numer telefonu");this.error_list.phone=true;
            }else if(this.phone.length!=9){
                isOk=false; this.errors.push("Nieprawidłowy numer telefonu");this.error_list.phone=true;
            }
            if(this.text.length>250){
                isOk=false; this.errors.push("Za długi komentarz");this.error_list.text=true;
            }

            return isOk;
      },

    //     wypelnijFormularz(){
    //         this.name = "Adam";
    //         this.surname = "Nowak";
    //         this.country = "Polska";
    //         this.address = "Rolna,35";
    //         this.zip_code = "43-124";
    //         this.city = "Nowa wieś";
    //         this.phone= "111222333";
    //         this.statute = true;
    //         this.decodeAddress();
    //         this.deliver_type = "Paczkomat";
    //         this.payment_type = "PayU";
    //         this.recaptchaDisabled = false;
    //   } 
  },
  computed:{
      chosePayment(){
          this.clearPayment();
          this.countDeliver();
          this.countTotal();
          return this.total;
      },

  },
}
</script>

<template>
<!-- <button @click="wypelnijFormularz()" >Wypełnij formularz</button> Usunąć, dodane dla zaoszczędzenia czasu ! -->
<Transition name="popup">
<div class="popup" v-if="show_login_form">
    <div class="container">
        <div class="top">
            <button @click="show_login_form=false">X</button>
        </div>
        <div class="bottom">
            <input type="text" placeholder="Login">
            <input type="password" placeholder="Hasło">
            <button @click="notImplemented()">ZALOGUJ</button>
        </div>
    </div>
</div>
</Transition >
<Transition name="popup">
<div class="popup" v-if="show_discount_form">
    <div class="container">
        <div class="top">
            <button @click="show_discount_form=false">X</button>
        </div>
        <div class="bottom">
            <input type="text" placeholder="XX-XXX-XXX" v-model="user_discount">
            <button @click="tryGetDiscount()">DODAJ</button>
            <p>{{discount_result}}</p>
        </div>
    </div>
</div>
</Transition >
<Transition name="popup">
<div class="popup" v-if="show_order_popup">
    <div class="container">
        <div class="top">
            <button @click="show_order_popup=false">X</button>
        </div>
        <div class="bottom">
            <div class="title">
                <p>
                    {{order_title}}
                </p>
            </div>
            <div class="code">
                <p>
                   {{generated_code}}
                </p>
            </div>
        </div>
    </div>
</div>
</Transition >
<div class="container">
    <div class="box">
        <div class="title-div">
            <p><span><span class="person">aaa</span></span>1. TWOJE DANE</p>
        </div>
        <button class="btn-log btn" @click="show_login_form=true">Logowanie</button>
        <p>
            <center>Masz już konto? Kliknij żeby się zalogować.</center>
        </p>
        <p>
            <input type="checkbox" v-model="create_account" />Stwórz nowe konto
        </p>
        <div>
            <Transition name="slide">
            <div v-if="create_account">
                <input type="text" :class="{ error: error_list.login}" placeholder="Login" v-model="login"/>                
                <input type="password" :class="{ error: error_list.password}" v-model="password" placeholder="Hasło"/>
                <input type="password" :class="{ error: error_list.password}" v-model="repeat_password" placeholder="Potwierdź hasło"/>
            </div>
            </Transition>
            <input type="text" :class="{ error: error_list.name}" placeholder="Imię*" v-model="name"/>
            <input type="text" :class="{ error: error_list.surname}" placeholder="Nazwisko*" v-model="surname"/>
            <select v-model="country">
                <option>Polska</option>
                <option>Niemcy</option>
            </select>
            <input type="text" :class="{ error: error_list.street}" placeholder="Ulica,numer domu*" v-model="address" @change="decodeAddress()"/>
            <div class="post-div">
            <input type="text" :class="{ error: error_list.zip_code}" placeholder="Kod pocztowy*" v-model="zip_code"/>
            <input type="text" :class="{ error: error_list.city}" placeholder="Miasto*" v-model="city"/> 
            </div>
            <input type="text" :class="{ error: error_list.phone}" placeholder="Telefon*" v-model="phone" @keyup="setPhone()"/>
            <input type="checkbox" v-model="another_location" />Dostawa pod inny adres
            <Transition name="slide">
            <div v-if="another_location">
                <select v-model="another_country">
                    <option>Polska</option>
                    <option>Niemcy</option>
                </select>
                <input type="text" :class="{ error: error_list.another_street}" placeholder="Ulica,numer domu*" v-model="another_address" @change="decodeAddress()"/>
                <div class="post-div">
                <input type="text" :class="{ error: error_list.another_zip_code}" placeholder="Kod pocztowy*" v-model="another_zip_code"/>
                <input type="text" :class="{ error: error_list.another_city}" placeholder="Miasto*" v-model="another_city"/> 
                </div>
            </div>
            </Transition>
        </div>
    </div>
    <div class="box">
        <div>
            <div class="title-div">
                <p><span><span class="deliver">aaa</span></span>2. METODA DOSTAWY</p>
            </div>
            <div class="deliver">
                <div>
                    <p>
                        <input type="radio" value="Paczkomat" v-model="deliver_type" @click="chosePayment()"/>
                        <span><span class="inpost">aaa</span></span>
                        Paczkomaty 24/7
                    </p>
                    <p class="right">10,99 zł</p>
                </div>
                <div>
                    <p>
                    <input type="radio" value="Kurier" v-model="deliver_type" @click="chosePayment()"/>
                    <span><span class="dpd">aaa</span></span>
                    Kurier DPD
                    </p>
                    <p class="right">18,00 zł</p>
                </div>
                <div>
                    <p>
                    <input type="radio" value="Kurier za pobraniem" v-model="deliver_type" @click="chosePayment()"/>
                    <span><span class="dpd">aaa</span></span>
                    Kurier DPD za pobraniem
                    </p>
                    <p class="right">22,00 zł</p>
                </div>
            </div>
        </div>
        <div>
            <div class="title-div">
                <p><span><span class="payment">aaa</span></span>3. METODA PŁATNOŚĆI</p>
            </div>
            <div>
                <Transition name="slide">
                <p v-show="deliver_type!='Kurier za pobraniem'">
                    <input type="radio" value="PayU" v-model="payment_type" />
                    <span><span class="payu">aaa</span></span>
                    PayU
                </p>
                </Transition>
                <Transition name="slide">
                <p v-show="deliver_type!='Kurier za pobraniem'">
                    <input type="radio"  value="Przelew bakowy" v-model="payment_type"/>
                    <span><span class="transfer">aaa</span></span>
                    Przelew bankowy - zwykły
                </p>
                </Transition>
                <Transition name="slide">
                <p v-show="deliver_type=='Kurier za pobraniem'||deliver_type==''">
                    <input type="radio"  value="Przy odbiorze" v-model="payment_type"/>
                    <span><span class="cash">aaa</span></span>
                    Płatność przy odbiorze
                </p>
                </Transition>
            </div>
            <button class="btn-code btn" @click="show_discount_form=true">
                    Dodaj kod rabatowy
            </button>
        </div>
    </div>
    <div class="box bottom-box">
        <div class="title-div">
            <p><span><span class="result">aaa</span></span>4. PODSUMOWANIE</p>
        </div>
        <div class="summary-top">
            <div class="left">
                <img src="../img/product.png" alt="ikona produktu"/>
            </div>
            <div class="right">
                <div class="line">
                    <p>Testowy produkt</p>
                    <p class="r">{{base}} zł</p>
                </div>
                <p>Ilość:1</p>
            </div>
        </div>
        <div class="summary-bottom">
            <div class="line">
                <p>Produkt</p><p class="r">{{base}} zł</p>
            </div>
            <div class="line">
                <p>Dostawa: {{deliver_type}}</p><p class="r">{{deliver}} zł</p>
            </div>
            <div class="line">
                <p>Płatność: </p><p class="r">{{payment_type}}</p>
            </div>
            <div class="line">
                <p>Rabat:</p><p class="r">{{discount}}%</p>
            </div>
            <div class="line b">
                <p>Łącznie</p><p class="r">{{chosePayment}} zł</p>
            </div>
            <div class="delivery">
                <p class="delivery-title">Dane do wysyłki</p>
                <div v-if="!another_location">
                    <p>{{country}}</p>
                    <p>{{street}} {{house_number}}</p>
                    <p>{{zip_code}} {{city}}</p>
                </div>
                <div v-if="another_location">
                    <p>{{another_country}}</p>
                    <p>{{another_street}} {{another_house_number}}</p>
                    <p>{{another_zip_code}} {{another_city}}</p>
                </div>
                
            </div>
        </div>
        <div>
            <textarea class="text" placeholder="Komentarz" v-model="text"></textarea>
        </div>
        <p>
            <input type="checkbox" v-model="newsletter"/>Zapisz się aby otrzymywać newsletter
        </p>
        <p>
            <input type="checkbox" v-model="statute"/>Zapoznałam/em z <a href="_blank">Regulaminem</a> zakupów*
        </p>
        <form action="?" method="POST">
            <div class="g-recaptcha" data-sitekey="6Lfxfl0gAAAAAOIupaOkUkeTBFhY2qGXxyv8MVsJ" data-callback="myRecaptchaMethod"></div>
        </form>
        <button class="btn-accept btn" @click="trySendOrder()" >
                POTWIERDŹ ZAKUP
        </button>
        <Transition name="slide">
        <div v-if="errors.length>0">
            <ul class="error-list" >
            <li v-for="error in errors" v-bind:key="error">
                {{ error }}
            </li>
        </ul>
        </div>
        </Transition>
    </div>
</div>
</template>

<style lang="scss">
$grey : #a89f8f;
$red : #e54444;
$red-light : #e75353;
$blue : #5389bc;

$border-round : 7px;

    *{
        padding:0px;
        margin:0px;
        box-sizing:border-box;
        font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
    }
    .error{
        color: $red-light;
        font-weight: bold;
    }

    .popup-enter-active,
    .popup-leave-active {
    transition: opacity 0.5s ease;
    }
    .popup-enter-from,
    .popup-leave-to {
    opacity: 0;
    }
    .slide-enter-active,
    .slide-leave-active {
     transition: all 0.3s ease-out;
    }

    .slide-enter-from,
    .slide-leave-to {
     transform: translateX(20px);
     opacity: 0;
    }
    .popup{
        position: fixed;
        top:0px;
        width: 100%;
        height: 100vh;
        background-color: rgba(100,100,100,0.4);
        padding: 10px;
        .container{
            background-color: white;
            height: 210px;
            margin: 50px auto;
            border-radius: $border-round;
            .top{
                width: 100%;
                button{
                    width: 35px;
                    height: 35px;
                    float:right;
                    border-radius: $border-round;
                    margin: 5px;
                    color: $red;
                    border: 1px solid $red;
                }
                button:hover{
                    color: white;
                    background-color: $red;
                }
                height: 37px;
            }
            .bottom{
                width: 100%;
                input{
                    font-size: 1.2em;
                    padding: 5px;
                }
                input,button{
                    display: block;
                    width: 80%;
                    height: 35px;
                    margin: 15px auto;
                }
                button{
                    color: $red;
                    border: 1px solid $red;
                    border-radius: $border-round;
                }
                button:hover{
                    color: white;
                    background-color: $red;
                }
                p{
                    text-align: center;
                }
            }
            .title{
                font-size: 1.6em;
                font-weight: bold;
                text-align: center;
                padding: 15px;
            }
            .code{
                font-size: 1.1em;
                text-align: center;
            }
        }

    }
    button{
        background-color: white;
        display: block;
        width: 100%;
        height: 70px;
        font-size: 1.5em;
    }
    h1{
        margin: 10px;
    }
    span{
        color:rgba(0,0,0,0);
        width:40px;
        background-position: center; 
        background-repeat: no-repeat; 
        background-size:contain; 

        .transfer{
        background-image: url(../img/transfer.png);
        }
        .dpd{
        background-image: url(../img/dpd.png);
        }
        .inpost{
        background-image: url(../img/inpost.png);
        }
        .payu{
        background-image: url(../img/payu.png);
        }
        .cash{
        background-image: url(../img/cash.png);
        }
        .person{
        background-image: url(../img/person.png);
        }
        .deliver{
        background-image: url(../img/deliver.png);
        }
        .payment{
        background-image: url(../img/payment.png);
        }
        .result{
        background-image: url(../img/result.png);
        }
    }
    a{
        color: $blue;
        text-decoration: none;
    }
    .btn{
        text-align: center;
        border-radius: $border-round;
        margin:10px 0px;
    }
    .btn-log,.btn-accept:hover{
        color : $red;
        background-color: white;
        border: 3px $red solid;
    }
    .btn-code{
        color : $grey;
        border: 3px $grey solid;
    }
    .btn-code:hover{
        color : white;
        background-color: $grey;
    }
    .btn-accept,.btn-log:hover{
        color : white;
        background-color: $red;
        border: 3px $red solid;
    }
    .deliver{
        p{
            display: inline-block;
        }
        .right{
            float: right;
            font-weight: bold;
        }
    }
    .comment{
        height: 70px;
    }
    .summary-top{
        display: flex;
        height: 94px;
        .left{
            width: 200px;
            padding: 10px;
            img{
                height: 100%;
                width: 100%;
            }
        }
        .right{
            width: 100%;
            .line{
                p{
                    display: inline-block;
                    font-weight: bold;
                }
                .r{
                    float: right;
                }
            }
        }
    }
    .summary-bottom{
        border-top:1px dashed black;
        border-bottom:1px dashed black;
        .b{
            font-weight: bold;
            font-size: 1.2em;
        }
        .line{
            p{
                display: inline-block;
                margin: 5px;
            }
            .r{
                float: right;
            }
        }
        .delivery{
            .delivery-title{
                text-align: center;
                font-size: 1.1em;
            }
        }
    }
    .error-list{
        color:$red;
        list-style: none;
    }
    .box{
        margin : 10px;
        .title-div {
            background-color: $grey;
            color: white;
            padding: 10px;
            border-radius: $border-round;

            p{
                font-size: 1.2em;
            }
        }
        .post-div{
            display: flex;

            input{
                flex:1;
                margin:0px !important;
            }
        }

        p{
            margin: 15px 0;

            img{
                display: inline-block;
                height: 60%;
            }
        }

        textarea{
            resize:none;
            padding: 5px;
            margin: 10px 0;
            width: 100%;
        }

        input[type=text],input[type=password],select{
            width: 100%;
            display: block;
            padding: 5px;
            margin: 10px 0;
        }
        input[type=radio],input[type=checkbox]{
            display: inline;
            margin-inline-end: 5px;
        }
        input[type=checkbox]:checked,input[type=radio]:checked {
            accent-color: red;
        }
    }

    @media (min-width: 576px) {

    }

    @media (min-width: 768px) {
        .container{
            display:flex;
            flex-wrap: wrap;
        }
        .box{
            max-width: 498px;
            flex-grow:1;
        }
        .bottom-box{
            max-width: 100%;
        }
        .popup{
            .container{
                width: 600px;
            }
        }
    }

    @media (min-width: 992px) {
        .box{
            max-width: 600px;
            flex:1;
        }
    }

    @media (min-width: 1200px) {
        .box{
        flex:1;
        max-width: none;
        flex-grow:1;
        }
    }
</style>

