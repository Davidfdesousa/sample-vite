# sample-vite

Este é um projeto Vite com TypeScript configurado com semantic-release para automação de versionamento e publicação.

## 🚀 Recursos

- ⚡️ Vite para desenvolvimento rápido
- 🦾 TypeScript para tipagem estática
- 📦 Semantic Release para versionamento automático
- 🤖 GitHub Actions para CI/CD
- 📝 Changelog automático

## 📦 Instalação

```bash
npm install
```

## 🏃‍♂️ Scripts Disponíveis

```bash
# Desenvolvimento
npm run dev

# Build para produção
npm run build

# Preview da build
npm run preview

# Release manual (para testes locais)
npm run semantic-release
```

## 🔄 Semantic Release

Este projeto usa [semantic-release](https://github.com/semantic-release/semantic-release) para automação de versionamento, changelog e publicação.

### Convenção de Commits

Use a convenção de commits para que o semantic-release funcione corretamente:

- `feat:` - Nova funcionalidade (incrementa versão minor)
- `fix:` - Correção de bug (incrementa versão patch)
- `BREAKING CHANGE:` - Mudança que quebra compatibilidade (incrementa versão major)
- `docs:` - Apenas documentação
- `style:` - Mudanças de formatação
- `refactor:` - Refatoração de código
- `test:` - Adição ou correção de testes
- `chore:` - Tarefas de manutenção

### Configuração

O semantic-release está configurado em `.releaserc.mjs` e executa automaticamente quando há push para a branch `main`.

### Variáveis de Ambiente

Para executar localmente, configure:

```bash
export GITHUB_TOKEN=your_github_token
export NPM_TOKEN=your_npm_token
```

## 📄 Licença

MIT