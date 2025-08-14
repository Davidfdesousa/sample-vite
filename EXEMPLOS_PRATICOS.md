# 🎯 Semantic-Release: Exemplos Práticos e Cenários Reais

## 📋 Casos de Uso Comuns

### 1. 🚀 Projeto Novo (Do Zero)

#### Cenário: Criar um projeto React com semantic-release

```bash
# 1. Criar projeto
npx create-react-app meu-app
cd meu-app

# 2. Instalar semantic-release
npm install --save-dev semantic-release @semantic-release/changelog @semantic-release/git @semantic-release/github

# 3. Configurar package.json
# Remover "private": true e ajustar scripts

# 4. Criar .releaserc.mjs
# (usar a configuração do guia principal)

# 5. Primeiro commit
git add .
git commit -m "feat: configurar projeto React com semantic-release"

# 6. Testar
npx semantic-release --dry-run

# 7. Primeira release
npx semantic-release --no-ci
```

### 2. 📦 Biblioteca NPM

#### Cenário: Biblioteca JavaScript para publicação no NPM

```javascript
// .releaserc.mjs
export default {
  branches: ["main"],
  plugins: [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    [
      "@semantic-release/npm",
      {
        npmPublish: true,
        tarballDir: "dist",
      },
    ],
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json", "package-lock.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    "@semantic-release/github",
  ],
};
```

```json
{
  "name": "@meuuser/minha-lib",
  "version": "0.0.0",
  "description": "Minha biblioteca incrível",
  "main": "dist/index.js",
  "files": ["dist"],
  "scripts": {
    "build": "tsc",
    "prepublishOnly": "npm run build",
    "semantic-release": "semantic-release"
  },
  "publishConfig": {
    "access": "public"
  }
}
```

### 3. 🔧 Projeto Monorepo

#### Cenário: Múltiplos pacotes em um repositório

```javascript
// .releaserc.mjs
export default {
  branches: ["main"],
  plugins: [
    [
      "@semantic-release/commit-analyzer",
      {
        releaseRules: [
          { scope: "core", release: "minor" },
          { scope: "ui", release: "minor" },
          { scope: "utils", release: "patch" },
        ],
      },
    ],
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    [
      "@semantic-release/exec",
      {
        prepareCmd: "npm run build:all",
        publishCmd: "npm run publish:all",
      },
    ],
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "packages/*/package.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    "@semantic-release/github",
  ],
};
```

### 4. 🌊 Múltiplas Branches

#### Cenário: Releases em desenvolvimento, beta e produção

```javascript
// .releaserc.mjs
export default {
  branches: [
    "main",                              // Produção: 1.0.0
    { name: "beta", prerelease: true },  // Beta: 1.1.0-beta.1
    { name: "alpha", prerelease: true }, // Alpha: 1.2.0-alpha.1
  ],
  plugins: [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    [
      "@semantic-release/npm",
      {
        npmPublish: true,
      },
    ],
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    "@semantic-release/github",
  ],
};
```

## 📝 Exemplos de Commits e Versionamento

### Cenário 1: Bug Fix (PATCH)
```bash
# Versão atual: 1.2.3
git commit -m "fix: corrigir validação de email em formulários"
# Próxima versão: 1.2.4
```

### Cenário 2: Nova Feature (MINOR)
```bash
# Versão atual: 1.2.4  
git commit -m "feat: adicionar autenticação com Google OAuth"
# Próxima versão: 1.3.0
```

### Cenário 3: Breaking Change (MAJOR)
```bash
# Versão atual: 1.3.0
git commit -m "feat!: alterar estrutura da API de usuários

BREAKING CHANGE: A propriedade 'name' foi renomeada para 'fullName' em todos os endpoints de usuário."
# Próxima versão: 2.0.0
```

### Cenário 4: Múltiplos Commits
```bash
# Versão atual: 2.0.0

# Commit 1
git commit -m "docs: atualizar README com novos exemplos"

# Commit 2  
git commit -m "fix: corrigir erro de timeout na API"

# Commit 3
git commit -m "feat: adicionar filtros avançados na busca"

# Resultado: 2.1.0 (por causa do feat)
```

## 🔄 Workflows Complexos

### 1. CI/CD com Múltiplos Ambientes

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop, beta]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "18"
      - run: npm ci
      - run: npm run lint
      - run: npm run test:coverage
      - run: npm run build

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level high

  release-main:
    needs: [test, security]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
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
          node-version: "18"
      - run: npm ci
      - run: npm run build
      - name: Release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npx semantic-release

  release-beta:
    needs: [test, security]
    if: github.ref == 'refs/heads/beta'
    runs-on: ubuntu-latest
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
          node-version: "18"
      - run: npm ci
      - run: npm run build
      - name: Release Beta
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npx semantic-release --extends .releaserc.beta.js
```

### 2. Deploy Automático

```javascript
// .releaserc.mjs
export default {
  branches: ["main"],
  plugins: [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    [
      "@semantic-release/exec",
      {
        prepareCmd: "npm run build",
        publishCmd: "npm run deploy:production && npm run notify:slack",
      },
    ],
    [
      "@semantic-release/git",
      {
        assets: ["CHANGELOG.md", "package.json"],
        message: "chore(release): ${nextRelease.version} [skip ci]\\n\\n${nextRelease.notes}",
      },
    ],
    "@semantic-release/github",
  ],
};
```

## 🎨 Personalizações Avançadas

### 1. Changelog Personalizado

```javascript
// .releaserc.mjs
export default {
  plugins: [
    [
      "@semantic-release/release-notes-generator",
      {
        preset: "angular",
        presetConfig: {
          types: [
            { type: "feat", section: "🚀 Novas Funcionalidades" },
            { type: "fix", section: "🐛 Correções de Bugs" },
            { type: "docs", section: "📚 Documentação" },
            { type: "style", section: "💄 Melhorias de Interface" },
            { type: "refactor", section: "♻️ Refatorações" },
            { type: "perf", section: "⚡ Melhorias de Performance" },
            { type: "test", section: "✅ Testes" },
            { type: "chore", section: "🔧 Manutenção" },
            { type: "ci", section: "👷 CI/CD" },
          ],
        },
      },
    ],
    [
      "@semantic-release/changelog",
      {
        changelogFile: "CHANGELOG.md",
        changelogTitle: \`# 📋 Changelog

Todas as mudanças notáveis neste projeto serão documentadas neste arquivo.

O formato é baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.0.0/),
e este projeto adere ao [Semantic Versioning](https://semver.org/lang/pt-BR/).\`,
      },
    ],
  ],
};
```

### 2. Regras de Release Customizadas

```javascript
// .releaserc.mjs
export default {
  plugins: [
    [
      "@semantic-release/commit-analyzer",
      {
        preset: "angular",
        releaseRules: [
          // Documentação importante gera patch
          { type: "docs", scope: "README", release: "patch" },
          { type: "docs", scope: "api", release: "patch" },
          
          // Refatorações importantes geram patch
          { type: "refactor", scope: "core", release: "patch" },
          
          // Performance sempre gera minor
          { type: "perf", release: "minor" },
          
          // Dependências críticas geram minor
          { scope: "deps", subject: "*security*", release: "minor" },
          
          // Commits marcados como no-release não geram versão
          { scope: "no-release", release: false },
          
          // Breaking changes sempre major (independente do tipo)
          { breaking: true, release: "major" },
        ],
        parserOpts: {
          noteKeywords: ["BREAKING CHANGE", "BREAKING CHANGES", "BREAKING"],
        },
      },
    ],
  ],
};
```

### 3. Notificações Customizadas

```javascript
// .releaserc.mjs
export default {
  plugins: [
    // ... outros plugins
    [
      "@semantic-release/exec",
      {
        successCmd: \`
          echo "🎉 Nova versão \${nextRelease.version} publicada!"
          curl -X POST -H 'Content-type: application/json' \\
            --data '{"text":"🚀 Nova release: v\${nextRelease.version}\\n\${nextRelease.notes}"}' \\
            \${SLACK_WEBHOOK_URL}
        \`,
      },
    ],
  ],
};
```

## 🚨 Cenários de Emergência

### 1. Reverter Release Problemática

```bash
# 1. Identificar a tag problemática
git tag

# 2. Reverter para versão anterior
git revert <commit-da-release>

# 3. Criar hotfix
git commit -m "fix: reverter mudanças da v1.2.0 que causavam problemas"

# 4. Nova release será 1.2.1
npx semantic-release --no-ci
```

### 2. Forçar Versão Específica

```bash
# Em casos extremos, você pode criar uma tag manualmente
git tag v2.0.0
git push origin v2.0.0

# Próxima release será baseada nesta tag
```

### 3. Pular CI para Commits de Manutenção

```bash
# Commits que não devem gerar release
git commit -m "chore: atualizar dependências [skip ci]"
git commit -m "docs: corrigir typos [skip release]"
```

## 📊 Monitoramento e Métricas

### 1. Script para Estatísticas de Release

```bash
#!/bin/bash
# release-stats.sh

echo "📊 Estatísticas de Releases"
echo "=========================="

# Total de releases
total_releases=$(git tag | grep -E '^v[0-9]+\.[0-9]+\.[0-9]+$' | wc -l)
echo "🏷️  Total de releases: $total_releases"

# Última release
last_release=$(git describe --tags --abbrev=0)
echo "📅 Última release: $last_release"

# Commits desde última release
commits_since=$(git rev-list ${last_release}..HEAD --count)
echo "📝 Commits desde última release: $commits_since"

# Releases por mês (últimos 6 meses)
echo "📈 Releases por mês:"
git tag --sort=-creatordate | head -20 | while read tag; do
  date=$(git log -1 --format=%ai "$tag" | cut -d' ' -f1 | cut -d'-' -f1-2)
  echo "   $date: $tag"
done | sort | uniq -c
```

### 2. Health Check do Semantic-Release

```bash
#!/bin/bash
# health-check.sh

echo "🔍 Health Check - Semantic Release"
echo "================================="

# Verificar configuração
if [ -f ".releaserc.mjs" ] || [ -f ".releaserc.js" ] || [ -f ".releaserc.json" ]; then
  echo "✅ Arquivo de configuração encontrado"
else
  echo "❌ Arquivo de configuração não encontrado"
fi

# Verificar dependências
if npm list semantic-release > /dev/null 2>&1; then
  echo "✅ Semantic-release instalado"
else
  echo "❌ Semantic-release não instalado"
fi

# Verificar commits convencionais
conventional_commits=$(git log --oneline -10 | grep -E "^[a-f0-9]+ (feat|fix|docs|style|refactor|test|chore|perf|ci|build)(\(.+\))?: " | wc -l)
total_commits=10

if [ $conventional_commits -gt 5 ]; then
  echo "✅ Commits seguem convenção (${conventional_commits}/${total_commits})"
else
  echo "⚠️  Poucos commits convencionais (${conventional_commits}/${total_commits})"
fi

# Testar dry-run
if npx semantic-release --dry-run > /dev/null 2>&1; then
  echo "✅ Dry-run passou"
else
  echo "❌ Dry-run falhou"
fi
```

## 🎓 Dicas e Boas Práticas

### 1. Estrutura de Commit Ideal

```bash
# ✅ Excelente
git commit -m "feat(auth): adicionar login com redes sociais

- Implementar OAuth para Google, Facebook e GitHub
- Adicionar testes unitários para novos fluxos
- Atualizar documentação da API

Resolves #123"

# ✅ Bom
git commit -m "fix: corrigir validação de email em formulários"

# ⚠️  Aceitável
git commit -m "feat: nova funcionalidade de busca"

# ❌ Evitar
git commit -m "fix stuff"
git commit -m "update"
```

### 2. Configuração Progressive

```javascript
// Fase 1: Básico (só changelog e versionamento)
export default {
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
};

// Fase 2: Adicionar GitHub releases
// + "@semantic-release/github"

// Fase 3: Adicionar publicação NPM
// + "@semantic-release/npm"

// Fase 4: Adicionar notificações e deploy
// + plugins customizados
```

### 3. Debugging Avançado

```bash
# Ver análise detalhada de commits
npx semantic-release --dry-run --debug | grep -A 10 "Analyzing commit"

# Ver plugins carregados
npx semantic-release --dry-run --debug | grep "Loaded plugin"

# Ver configuração final
npx semantic-release --dry-run --debug | grep -A 20 "configuration"

# Testar apenas análise
npx semantic-release --dry-run | grep "Analysis of.*complete"
```

---

**🎯 Este guia cobre 90% dos cenários reais que você encontrará com semantic-release!**
