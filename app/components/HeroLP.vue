<template>
  <div class="hero bg-base-200 min-h-screen">
    <div class="hero-content text-center">
      <div class="max-w-md">
        <h1 class="text-5xl font-bold">Hello there</h1>
        <p class="py-6">
          Provident cupiditate voluptatem et in. Quaerat fugiat ut assumenda excepturi exercitationem
          quasi. In deleniti eaque aut repudiandae et a id nisi.
        </p>
        <div class="flex flex-wrap justify-center gap-3">
          <button class="btn btn-primary" @click="goToExample()">Go to Example</button>
          <button class="btn btn-secondary" @click="goToCadastro()">Ir para o Cadastro</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const router = useRouter()

function goToExample () {
  router.push('/example')
}

function goToCadastro () {
  router.push('/cadastro')
}
</script>