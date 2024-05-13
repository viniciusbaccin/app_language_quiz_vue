<template>
    <div>
        <h2>Resultado</h2>
<!--        <p>Você acertou {{ $route.params.acertos }} de {{ $route.params.total }} perguntas.</p>-->
        <button @click="$router.push({ name: 'quiz' })">Tentar Novamente</button>
    </div>
</template>

<script>
export default {
    name: "ResultsView.vue"
}
</script>


<style scoped lang="scss">

</style>