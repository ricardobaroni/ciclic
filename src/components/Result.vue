<template>
    <div class="mt-6">
        <div class="bg-white rounded-lg p-6">
            <div class="text-cente flex flex-col justify-center items-center">
                <h2 class="text-lg">Olá {{userResponse.name}}!</h2>
                <div class="text-gray-600 w-1/2 text-center">Juntando R${{userResponse.monthlyValue}} todo mês, 
                    você terá R${{userResponse.response}} em {{userResponse.year}}.
                </div>
            </div>
        </div>
        <span @click="$emit('back')" >
            <Button text="Simular Novamente" />
        </span>
    </div>
</template>

<script>
import Button from './ButtonSend.vue';

export default {
  name: 'Result',
    props:{
        userResponse:{
            type:Object,
        },
    },
    components: {
        Button,
    }
}
</script>

