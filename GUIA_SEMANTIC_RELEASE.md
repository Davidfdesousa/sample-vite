# 📦 Guia Completo: Semantic-Release do Zero

## 📋 Índice

1. [O que é o Semantic-Release](#o-que-é-o-semantic-release)
2. [Pré-requisitos](#pré-requisitos)
3. [Instalação Passo a Passo](#instalação-passo-a-passo)
4. [Configuração Básica](#configuração-básica)
5. [Convenção de Commits](#convenção-de-commits)
6. [Configuração Avançada](#configuração-avançada)
7. [GitHub Actions](#github-actions)
8. [Tokens e Autenticação](#tokens-e-autenticação)
9. [Primeira Release](#primeira-release)
10. [Troubleshooting](#troubleshooting)

---

## 🤔 O que é o Semantic-Release

O **Semantic-Release** é uma ferramenta que automatiza todo o processo de versionamento e publicação de pacotes, incluindo:

- 🔍 **Análise de commits** para determinar o tipo de release
- 📈 **Versionamento automático** seguindo Semantic Versioning (SemVer)
- 📝 **Geração de changelog** automático
- 🏷️ **Criação de tags Git** 
- 📦 **Publicação no NPM**
- 🚀 **Criação de releases no GitHub**

### 🎯 Benefícios

- ✅ **Zero configuração manual** de versões
- ✅ **Changelog sempre atualizado**
- ✅ **Processo padronizado** entre projetos
- ✅ **Integração com CI/CD**
- ✅ **Redução de erros humanos**

---

## 🛠️ Pré-requisitos

Antes de começar, certifique-se de ter:

- ✅ **Node.js** (versão 18 ou superior)
- ✅ **Git** configurado
- ✅ **Repositório Git** inicializado
- ✅ **package.json** básico
- ✅ **Conta no GitHub** (opcional, para releases)
- ✅ **Conta no NPM** (opcional, para publicação)

### Verificação dos Pré-requisitos

```bash
# Verificar Node.js
node --version

# Verificar NPM
npm --version

# Verificar Git
git --version

# Verificar se está em um repositório Git
git status
```

---

## 📦 Instalação Passo a Passo

### 1. Criar/Preparar o Projeto

```bash
# Se for um projeto novo
mkdir meu-projeto
cd meu-projeto
npm init -y

# Inicializar Git (se ainda não foi feito)
git init
git remote add origin https://github.com/usuario/meu-projeto.git
```

### 2. Instalar o Semantic-Release

```bash
# Instalar como dependência de desenvolvimento
npm install --save-dev semantic-release

# Instalar plugins essenciais
npm install --save-dev @semantic-release/changelog
npm install --save-dev @semantic-release/git
npm install --save-dev @semantic-release/github
npm install --save-dev @semantic-release/npm
```

### 3. Verificar Instalação

```bash
# Verificar se foi instalado corretamente
npx semantic-release --version
```

---

## ⚙️ Configuração Básica

### 1. Configurar package.json

Edite seu `package.json`:

```json
{
  "name": "meu-projeto",
  "version": "0.0.0",
  "description": "Descrição do projeto",
  "main": "index.js",
  "scripts": {
    "semantic-release": "semantic-release"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/usuario/meu-projeto.git"
  },
  "author": "Seu Nome",
  "license": "MIT",
  "devDependencies": {
    "semantic-release": "^24.0.0",
    "@semantic-release/changelog": "^6.0.0",
    "@semantic-release/git": "^10.0.0",
    "@semantic-release/github": "^10.0.0",
    "@semantic-release/npm": "^12.0.0"
  }
}
```

**⚠️ Importante**: 
- Remove `"private": true` se quiser publicar no NPM
- A versão deve ser `"0.0.0"` (será gerenciada automaticamente)

### 2. Criar Arquivo de Configuração

Para projetos **ES Modules** (type: "module"):

```javascript
// .releaserc.mjs
export default {
  branches: ["main"],
  plugins: [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    "@semantic-release/github",
  ],
  repositoryUrl: "https://github.com/usuario/meu-projeto",
};
```

Para projetos **CommonJS**:

```javascript
// .releaserc.js
module.exports = {
  branches: ["main"],
  plugins: [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator", 
    "@semantic-release/changelog",
    "@semantic-release/npm",
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    "@semantic-release/github",
  ],
  repositoryUrl: "https://github.com/usuario/meu-projeto",
};
```

---

## 📝 Convenção de Commits

O semantic-release funciona baseado na **Conventional Commits**:

### Tipos de Commit e Impacto na Versão

| Tipo | Descrição | Incremento | Exemplo |
|------|-----------|------------|---------|
| `fix:` | Correção de bug | **PATCH** (1.0.0 → 1.0.1) | `fix: corrigir erro na validação` |
| `feat:` | Nova funcionalidade | **MINOR** (1.0.0 → 1.1.0) | `feat: adicionar login social` |
| `BREAKING CHANGE:` | Mudança incompatível | **MAJOR** (1.0.0 → 2.0.0) | `feat!: alterar API de usuários` |
| `docs:` | Apenas documentação | **NENHUM** | `docs: atualizar README` |
| `style:` | Formatação/estilo | **NENHUM** | `style: corrigir indentação` |
| `refactor:` | Refatoração | **NENHUM** | `refactor: simplificar função` |
| `test:` | Testes | **NENHUM** | `test: adicionar teste unitário` |
| `chore:` | Manutenção | **NENHUM** | `chore: atualizar dependências` |

### 📋 Exemplos Práticos

```bash
# ✅ PATCH - Correção de bug
git commit -m "fix: corrigir erro de validação de email"

# ✅ MINOR - Nova funcionalidade  
git commit -m "feat: adicionar autenticação com Google"

# ✅ MAJOR - Breaking change
git commit -m "feat!: alterar estrutura da API de usuários

BREAKING CHANGE: A API agora retorna objetos diferentes"

# ✅ Documentação (não gera release)
git commit -m "docs: adicionar exemplos no README"

# ❌ ERRADO - Não segue convenção
git commit -m "corrigido bug"
git commit -m "novo feature"
```

---

## 🔧 Configuração Avançada

### 1. Configuração Personalizada de Plugins

```javascript
// .releaserc.mjs
export default {
  branches: [
    "main",
    { name: "beta", prerelease: true },
    { name: "alpha", prerelease: true }
  ],
  plugins: [
    // Análise de commits personalizada
    [
      "@semantic-release/commit-analyzer",
      {
        preset: "angular",
        releaseRules: [
          { type: "docs", scope: "README", release: "patch" },
          { type: "refactor", release: "patch" },
          { scope: "no-release", release: false },
        ],
      },
    ],
    
    // Geração de notas personalizadas
    [
      "@semantic-release/release-notes-generator",
      {
        preset: "angular",
        presetConfig: {
          types: [
            { type: "feat", section: "🚀 Funcionalidades" },
            { type: "fix", section: "🐛 Correções" },
            { type: "docs", section: "📝 Documentação" },
            { type: "style", section: "💄 Estilo" },
            { type: "refactor", section: "♻️ Refatoração" },
            { type: "test", section: "✅ Testes" },
            { type: "chore", section: "🔧 Manutenção" },
          ],
        },
      },
    ],
    
    // Changelog personalizado
    [
      "@semantic-release/changelog",
      {
        changelogFile: "CHANGELOG.md",
        changelogTitle: "# 📋 Changelog\\n\\nTodas as mudanças notáveis neste projeto serão documentadas neste arquivo.",
      },
    ],
    
    // Configuração NPM
    [
      "@semantic-release/npm",
      {
        npmPublish: true,
        tarballDir: "dist",
      },
    ],
    
    // Configuração Git
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json", "package-lock.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    
    // Configuração GitHub
    [
      "@semantic-release/github",
      {
        assets: [
          { path: "dist/*.tgz", label: "Pacote NPM" },
          { path: "dist/*.zip", label: "Código fonte" },
        ],
        successComment: "🎉 Esta issue foi resolvida na versão ${nextRelease.version}",
      },
    ],
  ],
  repositoryUrl: "https://github.com/usuario/meu-projeto",
};
```

### 2. Configuração por Ambiente

```javascript
// .releaserc.mjs
const config = {
  branches: ["main"],
  plugins: [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
  ],
  repositoryUrl: "https://github.com/usuario/meu-projeto",
};

// Adicionar plugins apenas em produção (CI)
if (process.env.CI) {
  config.plugins.push("@semantic-release/npm");
  config.plugins.push("@semantic-release/github");
}

export default config;
```

---

## 🤖 GitHub Actions

### 1. Criar Workflow

Crie o arquivo `.github/workflows/release.yml`:

```yaml
name: Release

on:
  push:
    branches:
      - main

permissions:
  contents: read

jobs:
  release:
    name: Release
    runs-on: ubuntu-latest
    
    # Permissões necessárias para o semantic-release
    permissions:
      contents: write        # Para criar tags e releases
      issues: write         # Para comentar em issues
      pull-requests: write  # Para comentar em PRs
      id-token: write      # Para provenance do NPM
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Buscar todo o histórico
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "lts/*"
          cache: "npm"
      
      - name: Install dependencies
        run: npm clean-install
      
      - name: Verify the integrity of provenance attestations and registry signatures
        run: npm audit signatures
      
      - name: Build (se necessário)
        run: npm run build --if-present
      
      - name: Run tests (se houver)
        run: npm test --if-present
      
      - name: Release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npx semantic-release
```

### 2. Workflow Mais Completo

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    name: Test
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "lts/*"
          cache: "npm"
      
      - run: npm ci
      - run: npm run lint --if-present
      - run: npm test --if-present
      - run: npm run build --if-present

  release:
    name: Release
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    permissions:
      contents: write
      issues: write
      pull-requests: write
      id-token: write
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          
      - uses: actions/setup-node@v4
        with:
          node-version: "lts/*"
          cache: "npm"
      
      - run: npm ci
      - run: npm run build --if-present
      
      - name: Release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npx semantic-release
```

---

## 🔑 Tokens e Autenticação

### 1. GitHub Token

O `GITHUB_TOKEN` é fornecido automaticamente pelo GitHub Actions, mas você pode criar um Personal Access Token para mais controle:

#### Criando um Personal Access Token:

1. Vá para **GitHub** → **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**
2. Clique em **Generate new token (classic)**
3. Defina as permissões:
   - ✅ `repo` (acesso completo ao repositório)
   - ✅ `write:packages` (para publicar pacotes)
4. Copie o token

#### Configurando no GitHub:

1. Vá para seu repositório → **Settings** → **Secrets and variables** → **Actions**
2. Clique em **New repository secret**
3. Nome: `GITHUB_TOKEN`
4. Valor: Seu token

### 2. NPM Token

Para publicar no NPM, você precisa de um token:

#### Criando NPM Token:

1. Faça login em [npmjs.com](https://www.npmjs.com)
2. Vá para **Account** → **Access Tokens**
3. Clique em **Generate New Token**
4. Escolha **Automation** (para CI/CD)
5. Copie o token

#### Configurando no GitHub:

1. Vá para seu repositório → **Settings** → **Secrets and variables** → **Actions**
2. Clique em **New repository secret**
3. Nome: `NPM_TOKEN`
4. Valor: Seu token NPM

### 3. Variáveis de Ambiente Locais

Para testes locais, crie um arquivo `.env` (nunca commite este arquivo!):

```bash
# .env (adicione ao .gitignore!)
GITHUB_TOKEN=seu_github_token_aqui
NPM_TOKEN=seu_npm_token_aqui
```

E adicione ao `.gitignore`:

```gitignore
# Environment variables
.env
.env.local
.env.*.local
```

---

## 🚀 Primeira Release

### 1. Preparar o Repositório

```bash
# Certifique-se de estar na branch main
git checkout main

# Certifique-se de ter pelo menos um commit seguindo convenção
git log --oneline

# Se não tiver commits convencionais, faça um:
git commit --allow-empty -m "feat: configurar semantic-release"
```

### 2. Testar Localmente (Dry Run)

```bash
# Testar sem fazer a release real
npx semantic-release --dry-run

# Ver o que seria gerado:
# - Qual versão seria criada
# - Quais commits seriam incluídos  
# - Qual changelog seria gerado
```

### 3. Executar a Primeira Release

#### Opção 1: Manualmente (local)

```bash
# Com tokens configurados no .env ou variáveis de ambiente
npx semantic-release --no-ci
```

#### Opção 2: Via GitHub Actions

```bash
# Fazer push para main (vai disparar o workflow)
git push origin main
```

### 4. Verificar Resultados

Após a execução, verifique:

- ✅ **Tag criada**: `git tag` deve mostrar a nova versão
- ✅ **CHANGELOG.md**: Arquivo criado com as mudanças
- ✅ **package.json**: Versão atualizada
- ✅ **GitHub Release**: Nova release no GitHub
- ✅ **NPM Package**: Pacote publicado (se configurado)

---

## 🛠️ Troubleshooting

### Problemas Comuns e Soluções

#### 1. **Erro: "No commits found"**

```
No git tag version found on branch main
No previous release found, retrieving all commits
Found 0 commits since last release
```

**Solução**: Não há commits que sigam a convenção. Faça um commit válido:

```bash
git commit --allow-empty -m "feat: primeira funcionalidade"
```

#### 2. **Erro: "Invalid GitHub token"**

```
EINVALIDGHTOKEN Invalid GitHub token
```

**Solução**: Verifique se o token está correto e tem as permissões necessárias:

```bash
# Testar o token
curl -H "Authorization: token SEU_TOKEN" https://api.github.com/user
```

#### 3. **Erro: "No npm token specified"**

```
ENONPMTOKEN No npm token specified
```

**Soluções**:
- Configure o `NPM_TOKEN` ou
- Remova o plugin `@semantic-release/npm` se não quiser publicar

#### 4. **Erro: "Cannot require() ES Module"**

```
Cannot require() ES Module .releaserc.js
```

**Solução**: Use `.releaserc.mjs` para projetos ES Module ou `.releaserc.js` para CommonJS.

#### 5. **Workflow não executa**

**Verificações**:
- ✅ Arquivo está em `.github/workflows/`
- ✅ Sintaxe YAML está correta
- ✅ Push foi feito na branch `main`
- ✅ GitHub Actions está habilitado no repositório

#### 6. **Erro: "Push declined due to repository rule violations"**

```
Push cannot contain secrets
```

**Solução**: Remove tokens do código e use variáveis de ambiente:

```bash
# Remover arquivo com tokens
git rm .env
git commit -m "fix: remove sensitive tokens"

# Adicionar ao .gitignore
echo ".env" >> .gitignore
git add .gitignore
git commit -m "chore: add .env to .gitignore"
```

### Comandos de Debug

```bash
# Ver logs detalhados
npx semantic-release --debug

# Testar configuração
npx semantic-release --dry-run --debug

# Verificar plugins carregados
npx semantic-release --dry-run | grep "Loaded plugin"

# Verificar análise de commits
npx semantic-release --dry-run | grep "Analysis of"
```

---

## 📚 Recursos Adicionais

### Documentação Oficial
- [Semantic-Release](https://semantic-release.gitbook.io/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)

### Plugins Úteis
- [@semantic-release/exec](https://github.com/semantic-release/exec) - Executar comandos customizados
- [@semantic-release/slack](https://github.com/juliuscc/semantic-release-slack-bot) - Notificações no Slack  
- [@semantic-release/discord](https://github.com/Fdawgs/semantic-release-discord) - Notificações no Discord

### Ferramentas Complementares
- [Commitizen](https://github.com/commitizen/cz-cli) - Interface para commits convencionais
- [Commitlint](https://github.com/conventional-changelog/commitlint) - Validação de commits
- [Husky](https://github.com/typicode/husky) - Git hooks

---

## 🎯 Resumo dos Comandos

```bash
# Instalação completa
npm install --save-dev semantic-release @semantic-release/changelog @semantic-release/git @semantic-release/github @semantic-release/npm

# Primeiro teste
npx semantic-release --dry-run

# Primeira release (local)
npx semantic-release --no-ci

# Debug
npx semantic-release --debug --dry-run

# Verificar versão
npx semantic-release --version

# Ver tags criadas
git tag

# Ver commits desde última release
git log $(git describe --tags --abbrev=0)..HEAD --oneline
```

---

**🎉 Pronto! Agora você tem um guia completo para implementar o Semantic-Release em qualquer projeto!**
