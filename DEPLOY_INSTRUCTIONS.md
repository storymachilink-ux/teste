# 🚀 Instruções de Deploy - EdukaPrime

## ⚠️ IMPORTANTE: Leia antes de cada deploy!

Este documento contém instruções cruciais para garantir que os deploys não quebrem a experiência dos usuários logados.

## 🔧 Como Fazer Deploy Seguro

### 1. **ANTES de cada deploy:**

Abra o arquivo `src/utils/deploymentManager.ts` e **atualize a versão**:

```typescript
// IMPORTANTE: Atualize este número a cada deploy
export const APP_VERSION = '1.0.1'; // ← Incremente aqui
```

### 2. **Exemplos de versionamento:**

- **Pequenas correções**: `1.0.0` → `1.0.1`
- **Novas funcionalidades**: `1.0.1` → `1.1.0`
- **Mudanças importantes**: `1.1.0` → `2.0.0`

### 3. **Processo completo:**

```bash
# 1. Atualizar versão no deploymentManager.ts
# 2. Fazer build
npm run build

# 3. Fazer deploy
# (seu processo de deploy aqui)

# 4. Verificar logs no console do navegador
```

## 🛡️ O que o Sistema Faz Automaticamente

### Quando um usuário acessa após deploy:

1. **Detecta nova versão** comparando com versão armazenada
2. **Limpa dados de sessão** automaticamente
3. **Preserva configurações** importantes (tema, idioma)
4. **Mostra notificação** explicando a atualização
5. **Força novo login** para evitar erros

### Dados que são LIMPOS:
- ✅ Tokens de autenticação
- ✅ Dados de sessão do Supabase
- ✅ Cache de usuário
- ✅ Dados temporários

### Dados que são PRESERVADOS:
- ✅ Tema (escuro/claro)
- ✅ Idioma preferido
- ✅ Configurações de interface

## 🔍 Como Verificar se Funcionou

### No Console do Navegador:
```
🚀 [DEPLOYMENT MANAGER] Iniciando verificação de versão...
🔍 [VERSION CHECK] {stored: "1.0.0", current: "1.0.1", isFirstVisit: false}
🚨 [VERSION MISMATCH] Deploy detectado, invalidando sessões...
🔄 [DEPLOY DETECTED] 1.0.0 → 1.0.1
🧹 [CLEARING USER DATA] Limpando dados de sessão...
✅ [USER DATA CLEARED] Dados de sessão limpos, configurações preservadas
```

### Notificação para o Usuário:
Uma notificação aparecerá no canto superior direito:
```
🚀 EdukaPrime foi atualizado!
Faça login novamente para acessar as novas funcionalidades.
```

## 🚨 Cenários de Emergência

### Se algo der errado:

1. **Usuários não conseguem logar**:
   - Verifique se a versão foi atualizada corretamente
   - Verifique logs no console

2. **Força reset manual**:
   ```javascript
   // No console do navegador do usuário:
   localStorage.clear();
   sessionStorage.clear();
   location.reload();
   ```

3. **Rollback de versão**:
   - Volte a versão no código
   - Faça novo deploy

## 📊 Monitoramento

### Logs importantes para acompanhar:
- `🚀 [DEPLOYMENT MANAGER]` - Inicialização
- `🚨 [VERSION MISMATCH]` - Deploy detectado
- `✅ [SESSION VALID]` - Login bem-sucedido
- `❌ [SESSION INVALID]` - Sessão invalidada

### Como debuggar problemas:
```javascript
// No console do navegador:
window.EdukaPrimeDebug = {
  version: DeploymentManager.getVersionInfo(),
  clearData: () => DeploymentManager.clearAllUserData(),
  forceRefresh: () => DeploymentManager.forceRefresh()
};
```

## 🎯 Benefícios do Sistema

### Para o Desenvolvedor:
- ✅ **Deploy sem medo**: Não quebra usuários logados
- ✅ **Notificação automática**: Usuários sabem que houve atualização
- ✅ **Logs detalhados**: Fácil debug de problemas
- ✅ **Configurações preservadas**: Boa experiência do usuário

### Para o Usuário:
- ✅ **Sem erros estranhos**: Não fica travado em telas quebradas
- ✅ **Transparência**: Sabe quando há atualizações
- ✅ **Login simples**: Apenas refaz login, dados pessoais preservados
- ✅ **Experiência fluida**: Sistema guia o processo

## 📝 Checklist de Deploy

- [ ] Versão atualizada em `deploymentManager.ts`
- [ ] Build realizado com sucesso
- [ ] Deploy feito
- [ ] Teste realizado em navegador limpo
- [ ] Verificação de logs no console
- [ ] Confirmação de notificação de atualização

## 🔄 Exemplo Prático

### Deploy da versão 1.0.5 para 1.0.6:

1. **Antes do deploy:**
   ```typescript
   export const APP_VERSION = '1.0.6'; // Era 1.0.5
   ```

2. **Usuário acessa após deploy:**
   - Sistema detecta mudança `1.0.5 → 1.0.6`
   - Limpa sessão automaticamente
   - Mostra notificação de atualização
   - Usuário faz login normalmente

3. **Resultado:**
   - ✅ Zero quebras para usuários
   - ✅ Transição suave entre versões
   - ✅ Logs claros para debug

---

## ⚡ Lembrete Final

**SEMPRE atualize `APP_VERSION` antes de cada deploy!**

Este é o único passo manual necessário para garantir que o sistema funcione perfeitamente.