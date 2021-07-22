<script setup>
import { defineProps, reactive } from 'vue'
import axios from 'axios';

defineProps({});

data: {
    responseData: null
    codeResponse: null
}

function authenticate(){
    var user = document.getElementById("user_email");
    if(typeof(user) == 'undefined' || user == null){
        // TODO: Throw an error, input element missing or malformed.
        // Could add more validation for email addr, but this is handled by the API anyway.
        this.updateStatus();
        return;
    }
    
    var email = user.value; // Email validation handled on API side. Ignore for now.
    
    var headers = {
        'Accept': 'application/json',
        'Content-Type': 'application/x-www-form-urlencoded'
    }

    var params = {
        api_key: 'f0e1ce6078f8e8cbfc70821fcec04f56', // MUST OBFUSCATE THIS WITH .ENV
        email: email
    }

    // Send request to mod.io API to authenticate user.
    axios.post('https://api.mod.io/v1/oauth/emailrequest', {}, {
        headers: headers,
        params: params
    })
    .then( response => {
        console.log(response);
        this.responseData = response;
    })
    .catch( error => {
        this.responseData = error.response;
    })
    .finally( () => {
        this.updateStatus();
    })
}

function updateStatus(){
    var errorBox = document.getElementsByClassName("error-message")[0];
    if(typeof(errorBox) == 'undefined' || errorBox == null){
        // TODO: Throw an error, input element missing or malformed.
        return;
    }

    if (this.responseData == null){
        errorBox.classList.add("active");
        errorBox.innerText = "An unknown error has occured. Please try again.";
        return;
    }

    if (this.responseData.data.error){
        errorBox.classList.add("active");
        if(this.responseData.data.error.message){
            errorBox.innerText = this.responseData.data.error.message;

            // Loop through any error information to display to user
            if (this.responseData.data.error.errors){
                Object.entries(this.responseData.data.error.errors).forEach(([key, value]) => {
                    errorBox.innerText += `\n ${key}: ${value}`;
                });
            }
            return;
        }
        errorBox.innerText = "An unknown error has occured. Please try again.";
        return;
    }

    // If a success
    if (this.responseData.data.code == 200){
        errorBox.classList.add("active", "success");

        if (this.responseData.data.message){
            errorBox.innerText = this.responseData.data.message;
        }
        else{
            errorBox.innerText = "Success!";
        }
        return;
    }

    //No error, no success.
    errorBox.classList.add("active");
    errorBox.innerText = "An unknown error has occured";

}

function validate_code(){
    //TODO: This should probably automatically fail if the user hadn't previously entered an email

    var digitCode = document.getElementById("digit_code");
    if(typeof(digitCode) == 'undefined' || digitCode == null){
        // TODO: Throw an error, input element missing or malformed.
        return;
    }
    var authCode = digitCode.value;

    var headers = {
        'Accept': 'application/json',
        'Content-Type': 'application/x-www-form-urlencoded'
    }

    var params = {
        api_key: 'f0e1ce6078f8e8cbfc70821fcec04f56', // MUST OBFUSCATE THIS
        security_code: authCode
    }

    // Send request to mod.io API to authenticate user.
    axios.post('https://api.mod.io/v1/oauth/emailexchange', {}, {
        headers: headers,
        params: params
    })
    .then( response => {
        console.log(response);
        this.responseData = response;
    })
    .catch( error => {
        this.responseData = error.response;
    })
    .finally( () => {
        this.updateStatus();
    })
}

</script>

<template>
    <div class="form-container">
        <h3>Please enter your email address to access Mod.IO</h3>
        <input id="user_email" type="text" placeholder="email" />
        <input type="submit" value="Request Access" @click="authenticate()"/>
    </div>
    <div class="security_code">
        <h3>Please enter your 5 digit security code</h3>
        <input type="text" id="digit_code" maxlength="5" minlength="5" />
        <input type="submit" value="Authenticate" @click="validate_code()"/>
    </div>
    <div class="error-message"></div>
</template>



<style scoped>
*{
    box-sizing:border-box;
}
a {
  color: #42b983;
}

input{
    width:80%;
    max-width:400px;
    padding:5px;
    text-transform:uppercase;
    display:block;
    margin:10px auto;
}

input[type="text"]{
    border-radius:5px;
    border:1px solid #aeaeae;
}

input[type="submit"]{
    background-color: #41b883;
    color:#fff;
    border:1px solid #41b883;
}

.form-container{
    width:100%;
    max-width:1000px;
    margin:0px auto;
}

.error-message{
    display:none;
    background-color:red;
    color:#fff;
    margin:0px auto;
    max-width:400px;
    font-size:11px;
    padding:5px 25px;
}

.error-message.active{
    display:block;
}

.error-message.success{
    background-color:green;
}
</style>
