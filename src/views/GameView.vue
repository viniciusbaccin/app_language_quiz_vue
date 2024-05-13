<template>
    <div class="container mt-5 d-flex flex-column align-items-center">
        <h1 class="text-center mb-5">Quiz de Tradução</h1>
        <div v-if="loading">
            <div class="spinner-border text-primary" role="status">
                <span class="visually-hidden">Carregando...</span>
            </div>
        </div>
        <div v-else-if="showResults">
            <div class="alert alert-success rounded-0" role="alert">
                <p>Fim do jogo! Você acertou {{ acertos }} de {{ questions.length }}.</p>
                <button @click="restartQuiz" class="btn btn-info mt-3 rounded-pill">Recomeçar</button>
            </div>
        </div>
        <div v-else-if="questions.length > 0">
            <h2>Qual é a tradução correta para "{{ currentQuestion?.word }}"?</h2>
            <div v-for="option in currentQuestion?.options" :key="option" class="form-check mb-2">
                <input class="form-check-input" type="radio" :id="option" :value="option" v-model="userAnswer">
                <label class="form-check-label" :for="option">{{ option }}</label>
            </div>
            <div class="d-flex justify-content-between mt-3">
                <button v-if="indexQuestion === questions.length - 1" @click="verifyAnswer" class="btn btn-primary rounded-pill">Verificar</button>
                <button v-else @click="nextQuestion" class="btn btn-secondary rounded-pill">Próxima</button>
            </div>
        </div>
        <div v-else>
            <p>Não foi possível carregar as perguntas.</p>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            questions: [],
            indexQuestion: 0,
            userAnswer: '',
            hits: 0,
            answers: [],
            showResults: false,
            loading: false, // Indica se a chamada para a API está em andamento
        };
    },

    mounted() {
        this.loadQuestions();
    },

    computed: {
        currentQuestion() {
            return this.questions[this.indexQuestion];
        }
    },

    methods: {
        loadQuestions() {
            this.loading = true; // Inicia o carregamento
            axios.get('http://localhost:8082/words')
                .then(response => {
                    console.log('Perguntas carregadas:', response.data)
                    if (response.data && response.data.length > 0) {
                        this.questions = response.data;
                        this.showResults = false;
                    } else {
                        // Se a resposta for null ou vazia, limpa as perguntas
                        this.questions = [];
                    }
                })
                .catch(error => {
                    console.error('Erro ao carregar as perguntas:', error);
                    this.questions = []; // Limpa as perguntas em caso de erro
                })
                .finally(() => {
                    this.loading = false; // Finaliza o carregamento
                });
        },
        nextQuestion() {
            this.answers.push(this.userAnswer);
            this.userAnswer = '';
            this.indexQuestion++;
        },
        verifyAnswer() {
            this.answers.push(this.userAnswer);
            this.questions.forEach((question, index) => {
                if (question.translation === this.answers[index]) {
                    this.hits++;
                }
            });
            this.showResults = true;
        },
        restartQuiz() {
            this.indexQuestion = 0;
            this.hits = 0;
            this.answers = [];
            this.userAnswer = '';
            this.loadQuestions(); // Recarrega as perguntas da API
        }
    },
};
</script>