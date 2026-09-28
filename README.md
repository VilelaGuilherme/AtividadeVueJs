# Nuxt Youtube + Cadastro

Projeto da atividade prática de **Node.js + Vue.js + Nuxt**.

Base: projeto desenvolvido no vídeo de Nuxt (Nuxt 4, Tailwind CSS 4 e DaisyUI 5, com SSR ligado).
Extensão: nova página `/cadastro` com formulário de cadastro de membro.

## Como rodar

Pré-requisito: Node.js 20+ instalado.

```bash
npm install
npm run dev
```

Abra http://localhost:3000

## Páginas
| Rota | Arquivo | Descrição |
|------|---------|-----------|
| `/` | `app/pages/index.vue` | Hero com botões de navegação |
| `/example` | `app/pages/example.vue` | Página de exemplo do vídeo |
| `/cadastro` | `app/pages/cadastro.vue` | Formulário de cadastro (nova) |

## Formulário (/cadastro)
- Nome completo (obrigatório, nome e sobrenome)
- E-mail (validação de formato)
- Curso / Área de atuação (select)
- Semestre (numérico, 1 a 12)
- Interesses / Habilidades (checkboxes, mínimo 1)
- Bio (textarea com limite de 200 caracteres e contador)

Ao enviar: alerta de sucesso na tela, `console.table` com os dados, lista dos cadastros da sessão e limpeza reativa dos campos.

## Conceitos usados
Roteamento baseado em arquivos (`app/pages/`), componentes auto-importados (`app/components/`), Composition API (`ref`, `reactive`, `computed`, `v-model`) e `useRouter`.
