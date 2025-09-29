# Integração Webhook AmploPay - EdukaPrime

## Configuração do Sistema de Autenticação

O sistema foi configurado para usar o token `21s2yh9n` como chave de acesso. Apenas usuários com este token específico têm acesso à área interna da plataforma.

## Estrutura do Webhook

### URL do Webhook (CONFIGURADA NO AMPLOPAY)
```
https://edukaprime.com.br/api/webhook/amplopay
```

### ⚠️ IMPORTANTE - Configurar no Painel AmploPay
1. Acesse o painel da AmploPay
2. Vá em Configurações → Webhooks
3. Adicione a URL: `https://edukaprime.com.br/api/webhook/amplopay`
4. Selecione o evento: `TRANSACTION_PAID`
5. Método: `POST`
6. Content-Type: `application/json`

### Payload Esperado
```json
{
  "event": "TRANSACTION_PAID",
  "token": "21s2yh9n",
  "offerCode": "LIGRMS3|ZMTP2IV|VBAQ4J3",
  "client": {
    "id": "string",
    "name": "string",
    "email": "string",
    "phone": "string",
    "cpf": "string",
    "cnpj": null,
    "address": {
      "country": "BR",
      "zipCode": "string",
      "state": "string",
      "city": "string",
      "neighborhood": "string",
      "street": "string",
      "number": "string",
      "complement": "string"
    }
  },
  "transaction": {
    "id": "string",
    "identifier": "string",
    "paymentMethod": "CREDIT_CARD|PIX|BOLETO",
    "status": "COMPLETED",
    "originalAmount": 0,
    "originalCurrency": "USD",
    "currency": "BRL",
    "exchangeRate": 0,
    "amount": 0,
    "createdAt": "ISO_DATE",
    "payedAt": "ISO_DATE",
    "boletoInformation": null,
    "pixInformation": null
  },
  "subscription": {
    "id": "string",
    "identifier": "string",
    "intervalCount": 1,
    "intervalType": "MONTHS",
    "startAt": "ISO_DATE",
    "cycle": 1,
    "status": "ACTIVE"
  },
  "orderItems": [
    {
      "id": "string",
      "price": 0,
      "product": {
        "id": "string",
        "name": "string",
        "externalId": "string"
      }
    }
  ],
  "trackProps": {
    "utm_source": "string",
    "utm_medium": "string",
    "utm_campaign": "string"
  }
}
```

## Mapeamento de Planos

| Offer Code | Plano | Checkout URL |
|------------|-------|--------------|
| LIGRMS3 | Essencial | https://checkout.amplopay.com/checkout/cmfyqhm0l02yqpuuw1k064a8d?offer=LIGRMS3 |
| ZMTP2IV | Evoluir | https://checkout.amplopay.com/checkout/cmfyqhm0l02yqpuuw1k064a8d?offer=ZMTP2IV |
| VBAQ4J3 | Prime | https://checkout.amplopay.com/checkout/cmfyqhm0l02yqpuuw1k064a8d?offer=VBAQ4J3 |

## Hierarquia de Acesso

- **Essencial** (nível 1): Acesso básico
- **Evoluir** (nível 2): Acesso a Essencial + funcionalidades Evoluir
- **Prime** (nível 3): Acesso total (Essencial + Evoluir + Prime)

## Sistema de Validação

### Token de Acesso
- Token fixo: `21s2yh9n`
- Verificação obrigatória em cada requisição
- Sem o token correto = sem acesso à plataforma

### Armazenamento Local
- Dados salvos no `localStorage` do navegador
- Chave: `edukaprime_user_access`
- Dados incluem: token, plano, email, nome, data de acesso

## Simulação de Desenvolvimento

Para desenvolvimento, há um simulador disponível no canto inferior direito da tela que permite:

1. **Simular Pagamento Essencial** - Concede acesso ao plano básico
2. **Simular Pagamento Evoluir** - Concede acesso ao plano intermediário
3. **Simular Pagamento Prime** - Concede acesso ao plano premium
4. **Limpar Acesso** - Remove todos os acessos para teste

## Implementação do Webhook Real

### Backend (Node.js/Express) - IMPLEMENTAÇÃO OBRIGATÓRIA
```javascript
// Instalar dependências necessárias:
// npm install express cors helmet express-rate-limit

const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');

const app = express();

// Middleware de segurança
app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '10mb' }));

// Rate limiting para webhooks
const webhookLimiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minuto
  max: 100, // máximo 100 requests por minuto
  message: 'Muitas requisições para webhook'
});

app.use('/api/webhook', webhookLimiter);

// ENDPOINT PRINCIPAL - https://edukaprime.com.br/api/webhook/amplopay
app.post('/api/webhook/amplopay', async (req, res) => {
  try {
    console.log('📨 Webhook recebido da AmploPay');

    const webhookData = req.body;

    // Log para monitoramento (remover dados sensíveis em produção)
    console.log('Dados do webhook:', {
      event: webhookData.event,
      email: webhookData.client?.email,
      offerCode: webhookData.offerCode,
      transactionId: webhookData.transaction?.id
    });

    // Validações obrigatórias
    if (!webhookData.token || webhookData.token !== '21s2yh9n') {
      console.error('❌ Token inválido ou ausente');
      return res.status(401).json({ error: 'Token inválido' });
    }

    if (webhookData.event !== 'TRANSACTION_PAID') {
      console.log('ℹ️ Evento ignorado:', webhookData.event);
      return res.status(200).json({ message: 'Evento ignorado' });
    }

    if (webhookData.transaction?.status !== 'COMPLETED') {
      console.log('ℹ️ Transação não completada:', webhookData.transaction?.status);
      return res.status(200).json({ message: 'Transação não completada' });
    }

    // Validar offer codes
    const validOffers = ['LIGRMS3', 'ZMTP2IV', 'VBAQ4J3'];
    if (!validOffers.includes(webhookData.offerCode)) {
      console.error('❌ Offer code inválido:', webhookData.offerCode);
      return res.status(400).json({ error: 'Offer code inválido' });
    }

    // PROCESSAR PAGAMENTO APROVADO
    await processApprovedPayment(webhookData);

    // Resposta de sucesso
    res.status(200).json({
      success: true,
      message: 'Pagamento processado com sucesso',
      timestamp: new Date().toISOString(),
      transactionId: webhookData.transaction.id
    });

  } catch (error) {
    console.error('❌ Erro no webhook:', error);
    res.status(500).json({
      error: 'Erro interno do servidor',
      timestamp: new Date().toISOString()
    });
  }
});

// Função para processar pagamento aprovado
async function processApprovedPayment(webhookData) {
  const { client, transaction, offerCode } = webhookData;

  console.log('✅ Processando pagamento aprovado:', {
    email: client.email,
    nome: client.name,
    plano: getplanName(offerCode),
    valor: transaction.amount
  });

  // AQUI VOCÊ DEVE IMPLEMENTAR:

  // 1. Salvar no banco de dados
  /*
  await database.users.create({
    email: client.email,
    name: client.name,
    plan: getplanType(offerCode),
    access_token: '21s2yh9n',
    transaction_id: transaction.id,
    activated_at: new Date(),
    expires_at: null // ou calcular data de expiração
  });
  */

  // 2. Enviar email de boas-vindas
  /*
  await sendWelcomeEmail({
    email: client.email,
    name: client.name,
    plan: getplanName(offerCode)
  });
  */

  // 3. Integrar com Supabase (se usado)
  /*
  await supabaseAdmin.from('users').upsert({
    email: client.email,
    plano_ativo: getplanNumber(offerCode),
    data_ativacao: new Date().toISOString()
  });
  */

  // 4. Log para auditoria
  console.log('📝 Acesso liberado:', {
    email: client.email,
    plano: getplanName(offerCode),
    timestamp: new Date().toISOString()
  });
}

// Funções auxiliares
function getplanName(offerCode) {
  const plans = {
    'LIGRMS3': 'Plano Essencial',
    'ZMTP2IV': 'Plano Evoluir',
    'VBAQ4J3': 'Plano Prime'
  };
  return plans[offerCode] || 'Plano Desconhecido';
}

function getplanType(offerCode) {
  const plans = {
    'LIGRMS3': 'essencial',
    'ZMTP2IV': 'evoluir',
    'VBAQ4J3': 'prime'
  };
  return plans[offerCode] || 'essencial';
}

function getplanNumber(offerCode) {
  const plans = {
    'LIGRMS3': 1, // Essencial
    'ZMTP2IV': 2, // Evoluir
    'VBAQ4J3': 3  // Prime
  };
  return plans[offerCode] || 1;
}

// Iniciar servidor
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`🚀 Servidor rodando na porta ${PORT}`);
  console.log(`📡 Webhook endpoint: https://edukaprime.com.br/api/webhook/amplopay`);
});
```

### Frontend (Integração com Checkout)
O sistema atual redireciona automaticamente para os links da AmploPay quando o usuário clica em "Assinar Agora". Após o pagamento, o webhook deve processar os dados e liberar o acesso.

## Segurança

1. **Validação do Token**: Sempre verificar se o token é `21s2yh9n`
2. **HTTPS Obrigatório**: Webhook deve usar HTTPS em produção
3. **Validação de IP**: Opcional - restringir IPs que podem chamar o webhook
4. **Rate Limiting**: Implementar limite de requisições por IP/minuto

## Logs Recomendados

- Todas as chamadas de webhook (sucesso e erro)
- Tentativas de acesso sem token válido
- Mudanças de plano de usuários
- Acessos à área restrita

## Monitoramento

- Verificar se webhooks estão sendo recebidos
- Monitorar taxa de conversão (pagamento → acesso liberado)
- Alertas para falhas no webhook
- Dashboard com métricas de pagamentos e acessos