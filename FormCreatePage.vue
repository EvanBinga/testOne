
<template>
    <div class="flex items-center justify-center h-screen">
        <form @submit.prevent="sendForm" class="
            border-8 border-gray-200 rounded-xl
            w-[500px] -mt-14 p-8
            flex flex-col gap-y-3
        ">
            <label class="flex flex-col gap-y-0.5 text-gray-500 focus-within:text-brand-2">
                <span class="text-lg">Заголовок</span>
                <input class="
                px-3 py-2 border-2 border-gray-300 rounded-md shadow-inner
                focus-visible:outline-brand-2 outline-2
            " type="text" v-model="data.title">
            </label>
            
            <input v-model="data.datetime" type="datetime-local" />

            <label class="flex flex-col gap-y-0.5 text-gray-500 focus-within:text-brand-2">
                <span class="text-lg">Описание</span>
                <textarea class="
                    px-3 py-2 border-2 border-gray-300 rounded-md shadow-inner
                    focus-visible:outline-brand-2 outline-2
                " rows="6" v-model="data.description" />
            </label>

            <!-- Добавленный селектор для выбора сервиса -->
            <label class="flex flex-col gap-y-0.5 text-gray-500 focus-within:text-brand-2">
                <span class="text-lg">Выберите сервис</span>
                <select v-model="data.service" class="
                    px-3 py-2 border-2 border-gray-300 rounded-md shadow-inner
                    focus-visible:outline-brand-2 outline-2
                ">
                    <option value="Магазин">Магазин</option>
                    <option value="Доставка">Доставка</option>
                    <option value="Приложение">Приложение</option>
                </select>
            </label>

        
            <!-- Добавленный 5-звездочный рейтинг -->
            <div class="flex gap-x-2 items-center">
                <span class="text-lg">Рейтинг:</span>
                <template v-for="star in 5">
                    <span 
                        @click="setRating(star)" 
                        :class="{ 'text-yellow-500': star <= rating, 'text-green-500': star <= rating }" 
                        class="cursor-pointer text-2xl"
                    >★</span>
                </template>
            </div>

            <button class="
                grid place-content-center w-full p-2 mt-1 border-2 border-gray-300 rounded-md shadow-sm outline-none bg-white
                text-lg font-semibold tracking-wide text-gray-400
                transition-all
                focus-visible:bg-brand-2 focus-visible:border-green-700 focus-visible:text-green-800 focus-visible:shadow-xl
                hover:bg-brand-2 hover:border-green-700 hover:text-green-800 hover:shadow-xl
            " type="submit">Отправить</button>
        </form>
    </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue';
import axios from 'axios';
import env from '@/env.json'
import router from '@/router';

const data = reactive({
    description: '',
    title: '',
    datetime: '',
    service: '', // Добавленное поле для выбора сервиса
});

const rating = ref(0); // Добавленная переменная для хранения рейтинга

const sendForm = async () => {
    try {
        const response = await sendFormImpl();
        if (!response.data.id)
            throw new Error('Ошибка сервера');
        await router.push({ name: 'feedback-show', params: { id: response.data.id } });
    } catch (error) {
        alert(error);
    }
}

const sendFormImpl = async () => {
    return await axios.post<StoreFeedbackResponse>(env.backend_url + '/feedbacks', {
        'description': data.description,
        'title': data.title,
        'datetime': new Date(data.datetime).getTime(),
        'service': data.service, // Добавленное поле для сервиса
        'rating': rating.value // Добавленное поле для рейтинга
    });
}

const setRating = (value) => {
    rating.value = value;
}


interface StoreFeedbackResponse {
    id: number
}


</script>
