<template>
    <div class="mt-2">
        <div v-if="!loading" class="rounded-md shadow-sm p-2">
            <div class="my-2">
                <input v-model="userData.name" aria-label="Nome" name="name" type="text" @keydown="count+=1" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:shadow-outline-blue focus:border-blue-300 focus:z-10 sm:text-sm sm:leading-5" placeholder="Nome do Contribuinte">
            </div>
            <div class="my-2 relative">
                <p v-if="userData.monthlyValue" class="text-gray-800 absolute z-10 currency">R$:</p>
                <input v-model="userData.monthlyValue" :class="userData.monthlyValue ? 'pl-8' :  '' " aria-label="mensalidade" name="mensalidade" type="number" @keydown="count+=1" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:shadow-outline-blue focus:border-blue-300 focus:z-10 sm:text-sm sm:leading-5" placeholder="Mensalidade em Reais">
            </div>
            <div class="my-2 flex items-center">
                <p class="text-gray-500 w-1/2 text-sm ml-2">Tempo de Contribuição:</p>
                <div class="relative flex justify-around items-center w-1/2">
                    <p @click="subYear" :class="changeBorder"  class="cursor-pointer px-3 py-2 border text-gray-500 rounded sm:text-sm sm:leading-5">&minus;</p>
                    <p class="text-gray-500">{{textYear}}</p>
                    <p @click="addYear" class="border-green-300 cursor-pointer px-3 py-2 border text-gray-500 rounded focus:outline-none focus:shadow-outline-blue focus:border-blue-300 focus:z-10 sm:text-sm sm:leading-5">&#43;</p>
                </div>
            </div>
        </div>
        <div class="flex" v-else>
            <div class="loader loader--style1" title="0">
                <svg version="1.1" id="loader-1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
                width="40px" height="40px" viewBox="0 0 40 40" enable-background="new 0 0 40 40" xml:space="preserve">
                <path opacity="0.2" fill="#000" d="M20.201,5.169c-8.254,0-14.946,6.692-14.946,14.946c0,8.255,6.692,14.946,14.946,14.946
                    s14.946-6.691,14.946-14.946C35.146,11.861,28.455,5.169,20.201,5.169z M20.201,31.749c-6.425,0-11.634-5.208-11.634-11.634
                    c0-6.425,5.209-11.634,11.634-11.634c6.425,0,11.633,5.209,11.633,11.634C31.834,26.541,26.626,31.749,20.201,31.749z"/>
                <path fill="#000" d="M26.013,10.047l1.654-2.866c-2.198-1.272-4.743-2.012-7.466-2.012h0v3.312h0
                    C22.32,8.481,24.301,9.057,26.013,10.047z">
                    <animateTransform attributeType="xml"
                    attributeName="transform"
                    type="rotate"
                    from="0 20 20"
                    to="360 20 20"
                    dur="0.5s"
                    repeatCount="indefinite"/>
                    </path>
                </svg>
            </div>
        </div>
        <span :key="count" @click="send()" >
            <Button text="Iniciar Simulação" icon="calculator" :disabled="validateButton" />
        </span>
    </div>
</template>

<script>
import Button from './ButtonSend.vue';  

export default {
  name: 'Simulator',
    data () {
        return {
            count:0,
            loading:false,
            userData:{
                year: 1,
                name:null,
                monthlyValue:null,
                response:null
            }
        }
    },
    computed:{
        textYear(){
            return this.userData.year > 1 ? `${this.userData.year} anos` : `${this.userData.year} ano`
        },
        changeBorder(){
            return this.userData.year > 1 ? "border-green-300" : "border-gray-300"
        },
        validateButton(){
            return this.userData.name && this.userData.monthlyValue ? true : false
        },
    },
    methods:{
        addYear(){
            this.userData.year += 1
        },
        subYear(){
            if(this.userData.year>1)
                this.userData.year -= 1  
        },
        send(){
            if(this.validateButton)
                    this.fetchAPI()
        },
        fetchAPI(){
            let months = this.userData.year * 12

            this.loading = true
            let _this = this;

                fetch("http://api.mathjs.org/v4/", {
                    method: 'post',
                    body: `{"expr":"${this.userData.monthlyValue} * (((1 + 0.00517) ^ ${months} - 1) / 0.00517)"}`
                })
                .then(
                    function(response) {
                        response.json().then(function(data) {
                            console.log(data.result);
                            _this.storeResult(data.result)
                        });
                    }
                )
                .catch(function(err) {
                    console.log('Fetch Error :-S', err);
                });
        },
        storeResult(result){
            this.userData.year = this.textYear
            this.userData.response = Math.round(result)

            this.$emit('store', this.userData)

            setTimeout(
                ()=>{ this.loading = false}, 
            2000);
        }
    },
    components: {
        Button,
    }
}
</script>

<style scoped>

.currency{
    top:6.1px;
    left:7px;
}

.loader{
    margin: 0 0 2em;
    height: 100px;
    width: 20%;
    text-align: center;
    padding: 1em;
    margin: 0 auto 1em;
    display: inline-block;
    vertical-align: top;
}

svg path,
svg rect{
    fill: #5a67d8;
}

</style>