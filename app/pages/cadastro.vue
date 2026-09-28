<template>
  <div class="min-h-screen bg-base-200 py-10 px-4">
    <div class="max-w-2xl mx-auto">
      <NuxtLink to="/" class="btn btn-ghost btn-sm mb-4">← Voltar para o início</NuxtLink>

      <div class="card bg-base-100 shadow-xl">
        <div class="card-body">
          <h1 class="card-title text-3xl">Novo membro</h1>
          <p class="opacity-70">Preencha os dados abaixo para se cadastrar.</p>

          <!-- Feedback visual de sucesso -->
          <div v-if="enviado" role="alert" class="alert alert-success mt-2">
            <span>✅ Cadastro realizado com sucesso! Os dados também foram enviados ao console.</span>
          </div>

          <form class="flex flex-col gap-4 mt-4" novalidate @submit.prevent="enviar">
            <!-- Nome -->
            <div class="form-control w-full">
              <label class="label" for="nome"><span class="label-text font-semibold">Nome completo *</span></label>
              <input
                id="nome"
                v-model="form.nome"
                type="text"
                placeholder="Ex: Maria da Silva"
                class="input input-bordered w-full"
                :class="{ 'input-error': tocados.nome && erros.nome }"
                @blur="tocar('nome')"
                @input="tocados.nome && validarCampo('nome')"
              />
              <p v-if="tocados.nome && erros.nome" class="text-error text-sm mt-1">{{ erros.nome }}</p>
            </div>

            <!-- E-mail -->
            <div class="form-control w-full">
              <label class="label" for="email"><span class="label-text font-semibold">E-mail *</span></label>
              <input
                id="email"
                v-model="form.email"
                type="email"
                placeholder="voce@email.com"
                class="input input-bordered w-full"
                :class="{ 'input-error': tocados.email && erros.email }"
                @blur="tocar('email')"
                @input="tocados.email && validarCampo('email')"
              />
              <p v-if="tocados.email && erros.email" class="text-error text-sm mt-1">{{ erros.email }}</p>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <!-- Curso -->
              <div class="form-control w-full">
                <label class="label" for="curso"><span class="label-text font-semibold">Curso / Área de atuação *</span></label>
                <select
                  id="curso"
                  v-model="form.curso"
                  class="select select-bordered w-full"
                  :class="{ 'select-error': tocados.curso && erros.curso }"
                  @blur="tocar('curso')"
                  @change="tocar('curso')"
                >
                  <option value="" disabled>Selecione...</option>
                  <option v-for="c in cursos" :key="c" :value="c">{{ c }}</option>
                </select>
                <p v-if="tocados.curso && erros.curso" class="text-error text-sm mt-1">{{ erros.curso }}</p>
              </div>

              <!-- Semestre -->
              <div class="form-control w-full">
                <label class="label" for="semestre"><span class="label-text font-semibold">Semestre / Período *</span></label>
                <input
                  id="semestre"
                  v-model.number="form.semestre"
                  type="number"
                  min="1"
                  max="12"
                  placeholder="1 a 12"
                  class="input input-bordered w-full"
                  :class="{ 'input-error': tocados.semestre && erros.semestre }"
                  @blur="tocar('semestre')"
                  @input="tocados.semestre && validarCampo('semestre')"
                />
                <p v-if="tocados.semestre && erros.semestre" class="text-error text-sm mt-1">{{ erros.semestre }}</p>
              </div>
            </div>

            <!-- Interesses -->
            <fieldset class="form-control">
              <legend class="label-text font-semibold mb-2">Interesses / Habilidades *</legend>
              <div class="flex flex-wrap gap-x-6 gap-y-2">
                <label v-for="a in areas" :key="a" class="flex items-center gap-2 cursor-pointer">
                  <input
                    v-model="form.interesses"
                    type="checkbox"
                    :value="a"
                    class="checkbox checkbox-primary checkbox-sm"
                    @change="tocar('interesses')"
                  />
                  <span>{{ a }}</span>
                </label>
              </div>
              <p v-if="tocados.interesses && erros.interesses" class="text-error text-sm mt-1">{{ erros.interesses }}</p>
            </fieldset>

            <!-- Bio -->
            <div class="form-control w-full">
              <label class="label" for="bio"><span class="label-text font-semibold">Mensagem / Bio curta</span></label>
              <textarea
                id="bio"
                v-model="form.bio"
                rows="4"
                placeholder="Fale um pouco sobre você..."
                class="textarea textarea-bordered w-full"
                :class="{ 'textarea-error': erros.bio }"
                @input="tocar('bio')"
              />
              <p class="text-sm mt-1" :class="restantes < 0 ? 'text-error' : 'opacity-60'">
                {{ restantes }} caracteres restantes
              </p>
              <p v-if="erros.bio" class="text-error text-sm">{{ erros.bio }}</p>
            </div>

            <button type="submit" class="btn btn-primary" :disabled="enviando">
              <span v-if="enviando" class="loading loading-spinner loading-sm" />
              {{ enviando ? 'Enviando...' : 'Cadastrar' }}
            </button>
          </form>
        </div>
      </div>

      <!-- Cadastros feitos nesta sessão (reatividade) -->
      <div v-if="membros.length" class="mt-8">
        <h2 class="text-xl font-bold mb-3">Cadastros nesta sessão ({{ membros.length }})</h2>
        <div v-for="(m, i) in membros" :key="i" class="card bg-base-100 shadow mb-3">
          <div class="card-body p-4">
            <p><strong>{{ m.nome }}</strong> — {{ m.email }}</p>
            <p class="opacity-70">{{ m.curso }} · {{ m.semestre }}º semestre</p>
            <div class="flex flex-wrap gap-2">
              <span v-for="t in m.interesses" :key="t" class="badge badge-primary badge-outline">{{ t }}</span>
            </div>
            <p v-if="m.bio" class="text-sm opacity-80">{{ m.bio }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
useHead({ title: 'Novo membro | Nuxt Youtube' })

const MAX_BIO = 200
const cursos = [
  'Ciência da Computação',
  'Engenharia de Software',
  'Sistemas de Informação',
  'Análise e Desenvolvimento de Sistemas',
  'Engenharia da Computação',
  'Outro',
]
const areas = ['Front-end', 'Back-end', 'Mobile', 'UI/UX', 'DevOps', 'Dados / IA', 'Segurança']
const campos = ['nome', 'email', 'curso', 'semestre', 'interesses', 'bio']

const estadoInicial = () => ({
  nome: '',
  email: '',
  curso: '',
  semestre: null,
  interesses: [],
  bio: '',
})

const form = reactive(estadoInicial())
const erros = reactive({})
const tocados = reactive({})
const enviado = ref(false)
const enviando = ref(false)
const membros = ref([])

const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/
const restantes = computed(() => MAX_BIO - form.bio.length)

function validarCampo (campo) {
  let msg = ''
  switch (campo) {
    case 'nome':
      if (!form.nome.trim()) msg = 'O nome completo é obrigatório.'
      else if (form.nome.trim().split(/\s+/).length < 2) msg = 'Informe nome e sobrenome.'
      break
    case 'email':
      if (!form.email.trim()) msg = 'O e-mail é obrigatório.'
      else if (!emailRegex.test(form.email)) msg = 'Informe um e-mail válido (ex: nome@dominio.com).'
      break
    case 'curso':
      if (!form.curso) msg = 'Selecione um curso.'
      break
    case 'semestre':
      if (form.semestre === null || form.semestre === '') msg = 'Informe o semestre.'
      else if (form.semestre < 1 || form.semestre > 12) msg = 'O semestre deve estar entre 1 e 12.'
      break
    case 'interesses':
      if (form.interesses.length === 0) msg = 'Marque pelo menos um interesse.'
      break
    case 'bio':
      if (form.bio.length > MAX_BIO) msg = `A bio deve ter no máximo ${MAX_BIO} caracteres.`
      break
  }
  erros[campo] = msg
  return !msg
}

function tocar (campo) {
  tocados[campo] = true
  validarCampo(campo)
}

async function enviar () {
  campos.forEach((c) => (tocados[c] = true))
  const todosValidos = campos.map(validarCampo).every(Boolean)
  if (!todosValidos) return

  enviando.value = true
  await new Promise((r) => setTimeout(r, 800)) // simula uma requisição

  const payload = {
    ...form,
    interesses: [...form.interesses],
    enviadoEm: new Date().toLocaleString('pt-BR'),
  }
  console.log('%c✅ Novo membro cadastrado', 'color: #22c55e; font-weight: bold')
  console.table(payload)
  membros.value.unshift(payload)

  enviando.value = false
  enviado.value = true

  // Limpeza reativa dos campos
  Object.assign(form, estadoInicial())
  campos.forEach((c) => {
    erros[c] = ''
    tocados[c] = false
  })

  setTimeout(() => (enviado.value = false), 5000)
}
</script>
