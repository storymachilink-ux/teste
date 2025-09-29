# ✅ Checklist para Produção - EdukaPrime + AmploPay

## 🚀 Configurações Obrigatórias

### 1. ⚠️ URGENTE - Configurar Webhook no Painel AmploPay
- [ ] Acessar painel AmploPay
- [ ] Ir em **Configurações → Webhooks**
- [ ] Adicionar URL: `https://edukaprime.com.br/api/webhook/amplopay`
- [ ] Selecionar evento: `TRANSACTION_PAID`
- [ ] Método: `POST`
- [ ] Content-Type: `application/json`
- [ ] Testar webhook com transação de teste

### 2. 🖥️ Implementar Servidor Backend
- [ ] Criar servidor Node.js/Express na URL `https://edukaprime.com.br`
- [ ] Implementar endpoint `/api/webhook/amplopay` (código fornecido no arquivo `WEBHOOK_INTEGRATION.md`)
- [ ] Instalar dependências: `express`, `cors`, `helmet`, `express-rate-limit`
- [ ] Configurar HTTPS com certificado SSL válido
- [ ] Configurar logs de segurança e monitoramento

### 3. 🔐 Segurança
- [ ] Verificar se o token `21s2yh9n` está sendo validado corretamente
- [ ] Implementar rate limiting para webhooks
- [ ] Configurar CORS adequadamente
- [ ] Adicionar middleware de segurança (helmet)
- [ ] Configurar logs de auditoria

### 4. 💾 Banco de Dados (Opcional mas Recomendado)
- [ ] Criar tabela para armazenar transações
- [ ] Criar tabela para controle de acesso de usuários
- [ ] Implementar backup automático
- [ ] Configurar índices para performance

### 5. 📧 Email (Recomendado)
- [ ] Configurar serviço de email (SendGrid, Mailgun, etc.)
- [ ] Criar template de boas-vindas
- [ ] Implementar envio automático após pagamento aprovado

## 🧪 Testes Obrigatórios

### Teste de Integração
- [ ] Fazer uma compra de teste real no AmploPay
- [ ] Verificar se webhook é recebido corretamente
- [ ] Verificar se acesso é liberado automaticamente
- [ ] Testar todos os 3 planos (Essencial, Evoluir, Prime)

### Teste de Segurança
- [ ] Testar webhook com token inválido (deve rejeitar)
- [ ] Testar webhook com dados malformados (deve rejeitar)
- [ ] Testar rate limiting (muitas requisições)
- [ ] Verificar logs de segurança

### Teste de Falhas
- [ ] Simular falha no servidor durante webhook
- [ ] Verificar se AmploPay retenta webhook
- [ ] Testar cenários de rede instável

## 📊 Monitoramento

### Logs Essenciais
- [ ] Log de todos os webhooks recebidos
- [ ] Log de acessos liberados
- [ ] Log de tentativas de acesso negadas
- [ ] Log de erros e exceções

### Métricas Importantes
- [ ] Taxa de conversão (pagamento → acesso liberado)
- [ ] Tempo de resposta do webhook
- [ ] Número de falhas por dia
- [ ] Usuários ativos por plano

### Alertas
- [ ] Configurar alerta para falhas no webhook
- [ ] Configurar alerta para alta taxa de erros
- [ ] Configurar alerta para downtime do servidor

## 🔧 Configurações Técnicas

### Servidor
```bash
# Exemplo de configuração PM2 para produção
pm2 start server.js --name "edukaprime-webhook"
pm2 startup
pm2 save
```

### Nginx (se usado)
```nginx
location /api/webhook/amplopay {
    proxy_pass http://localhost:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

### Variáveis de Ambiente
```bash
NODE_ENV=production
PORT=3000
WEBHOOK_TOKEN=21s2yh9n
DATABASE_URL=sua_url_do_banco
EMAIL_SERVICE_API_KEY=sua_chave_email
```

## 📝 Documentação

### Para a Equipe
- [ ] Documentar processo de deploy
- [ ] Documentar como monitorar webhooks
- [ ] Criar runbook para problemas comuns
- [ ] Documentar processo de backup

### Para Suporte
- [ ] Como verificar se usuário tem acesso
- [ ] Como liberar acesso manualmente (emergência)
- [ ] Como interpretar logs de webhook
- [ ] Contatos técnicos importantes

## 🚨 Plano de Contingência

### Se Webhook Falhar
1. Verificar logs do servidor
2. Testar conectividade com AmploPay
3. Verificar certificado SSL
4. Liberar acesso manualmente se necessário
5. Contactar suporte AmploPay se problema persistir

### Se Servidor Cair
1. Verificar status do servidor
2. Reiniciar serviços se necessário
3. Verificar logs de sistema
4. AmploPay vai retentar webhooks automaticamente
5. Processar webhooks perdidos após servidor voltar

## ✅ Validação Final

Antes de ir ao ar:
- [ ] Todos os itens acima foram implementados
- [ ] Testes passaram com sucesso
- [ ] Monitoramento está funcionando
- [ ] Equipe foi treinada
- [ ] Plano de contingência está documentado
- [ ] Backup dos dados está configurado

## 📞 Contatos de Emergência

- **Suporte AmploPay**: [inserir contato]
- **Administrador do Servidor**: [inserir contato]
- **Responsável Técnico**: [inserir contato]

---

**Data de Implementação**: ___________
**Responsável**: ___________
**Status**: ⏳ Pendente / ✅ Concluído