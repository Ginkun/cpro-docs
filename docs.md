# 📚 Documentação Completa da API - CheckoutPro Backend

## 🏗️ Visão Geral

Esta documentação detalha todos os endpoints da API do sistema CheckoutPro, uma plataforma completa para venda de produtos digitais com sistema de afiliados, cursos online e integração com Facebook Ads.

**Base URL:** `http://localhost:5001/api`

---

## 🔐 Autenticação

### POST `/auth/register`
**Descrição:** Registrar novo usuário

**Body:**
```json
{
  "name": "string",
  "email": "string",
  "password": "string",
  "role": "string" // opcional: 'admin', 'instructor', 'student'
}
```

**Resposta:**
```json
{
  "status": "success",
  "message": "Usuário criado com sucesso",
  "user": {
    "id": "uuid",
    "name": "string",
    "email": "string",
    "role": "string"
  },
  "token": "jwt_token"
}
```

### POST `/auth/login`
**Descrição:** Fazer login

**Body:**
```json
{
  "email": "string",
  "password": "string"
}
```

**Resposta:**
```json
{
  "status": "success",
  "message": "Login realizado com sucesso",
  "user": {
    "id": "uuid",
    "name": "string",
    "email": "string",
    "role": "string"
  },
  "token": "jwt_token"
}
```

### POST `/auth/forgot-password`
**Descrição:** Solicitar redefinição de senha

**Body:**
```json
{
  "email": "string"
}
```

### POST `/auth/reset-password`
**Descrição:** Redefinir senha com token

**Body:**
```json
{
  "token": "string",
  "newPassword": "string"
}
```

---

## 🛍️ Produtos

### GET `/products`
**Descrição:** Listar produtos com filtros

**Query Parameters:**
- `page`: número da página (padrão: 1)
- `limit`: itens por página (padrão: 10)
- `category`: filtrar por categoria
- `status`: filtrar por status
- `search`: buscar por nome/descrição
- `instructor_id`: filtrar por instrutor

**Resposta:**
```json
{
  "products": [
    {
      "id": "uuid",
      "name": "string",
      "description": "string",
      "price": "decimal",
      "category": "string",
      "status": "string",
      "instructor": {
        "id": "uuid",
        "name": "string"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "pages": 10
  }
}
```

### GET `/products/:id`
**Descrição:** Buscar produto específico

**Resposta:**
```json
{
  "id": "uuid",
  "name": "string",
  "description": "string",
  "price": "decimal",
  "category": "string",
  "status": "string",
  "checkout_config": {
    "allow_installments": true,
    "max_installments": 12
  },
  "seo_config": {
    "meta_title": "string",
    "meta_description": "string"
  },
  "instructor": {
    "id": "uuid",
    "name": "string"
  }
}
```

### POST `/products`
**Descrição:** Criar novo produto

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "name": "string",
  "description": "string",
  "price": "decimal",
  "category": "string",
  "instructor_id": "uuid",
  "checkout_config": {
    "allow_installments": true,
    "max_installments": 12
  }
}
```

### PUT `/products/:id`
**Descrição:** Atualizar produto

**Headers:** `Authorization: Bearer <token>`

### DELETE `/products/:id`
**Descrição:** Deletar produto

**Headers:** `Authorization: Bearer <token>`

### GET `/products/:id/stats`
**Descrição:** Estatísticas do produto

**Headers:** `Authorization: Bearer <token>`

**Resposta:**
```json
{
  "total_sales": 150,
  "total_revenue": 15000.00,
  "conversion_rate": 3.5,
  "active_affiliates": 25
}
```

---

## 🛒 Pedidos

### GET `/orders`
**Descrição:** Listar pedidos

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page`: número da página
- `limit`: itens por página
- `status`: filtrar por status
- `product_id`: filtrar por produto
- `customer_email`: filtrar por email do cliente

### GET `/orders/:id`
**Descrição:** Buscar pedido específico

**Headers:** `Authorization: Bearer <token>`

### POST `/orders`
**Descrição:** Criar novo pedido

**Body:**
```json
{
  "product_id": "uuid",
  "customer_name": "string",
  "customer_email": "string",
  "customer_phone": "string",
  "payment_method": "card|pix",
  "installments": 1,
  "partner_code": "string" // opcional
}
```

### PUT `/orders/:id`
**Descrição:** Atualizar pedido

**Headers:** `Authorization: Bearer <token>`

### POST `/orders/:id/cancel`
**Descrição:** Cancelar pedido

**Headers:** `Authorization: Bearer <token>`

---

## 💳 Pagamento com Cartão

### POST `/card-payment/process`
**Descrição:** Processar pagamento com cartão

**Body:**
```json
{
  "order_id": "uuid",
  "card_data": {
    "number": "string",
    "holder_name": "string",
    "exp_month": "string",
    "exp_year": "string",
    "cvv": "string"
  },
  "installments": 1
}
```

### POST `/card-payment/webhook`
**Descrição:** Webhook para receber eventos da plataforma de cartão

**Body:**
```json
{
  "event": "charge.succeeded|charge.failed|charge.captured|charge.voided|charge.refunded",
  "data": {
    "charge_id": "string",
    "order_id": "uuid",
    "status": "string",
    "amount": "decimal"
  }
}
```

### GET `/card-payment/test`
**Descrição:** Testar conectividade da API

### POST `/card-payment/simulate-webhook`
**Descrição:** Simular webhook (apenas desenvolvimento)

---

## 🎓 Cursos

### GET `/courses`
**Descrição:** Listar cursos

**Query Parameters:**
- `instructor_id`: filtrar por instrutor
- `category`: filtrar por categoria
- `status`: filtrar por status
- `student_id`: incluir progresso do aluno

### GET `/courses/:course_id`
**Descrição:** Buscar curso específico com módulos e aulas

**Query Parameters:**
- `student_id`: incluir progresso do aluno

### POST `/courses`
**Descrição:** Criar novo curso

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "title": "string",
  "description": "string",
  "instructor_id": "uuid",
  "category": "string",
  "level": "beginner|intermediate|advanced",
  "duration_hours": 10,
  "product_id": "uuid" // opcional
}
```

### PUT `/courses/:course_id`
**Descrição:** Atualizar curso

**Headers:** `Authorization: Bearer <token>`

### DELETE `/courses/:course_id`
**Descrição:** Deletar curso

**Headers:** `Authorization: Bearer <token>`

### POST `/courses/:course_id/modules`
**Descrição:** Criar módulo do curso

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "title": "string",
  "description": "string"
}
```

### PUT `/courses/:course_id/modules/:module_id`
**Descrição:** Atualizar módulo

**Headers:** `Authorization: Bearer <token>`

### DELETE `/courses/:course_id/modules/:module_id`
**Descrição:** Deletar módulo

**Headers:** `Authorization: Bearer <token>`

### POST `/courses/:course_id/modules/:module_id/lessons`
**Descrição:** Criar aula do módulo

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "title": "string",
  "content": "string",
  "video_url": "string",
  "duration_minutes": 30
}
```

### PUT `/courses/:course_id/modules/:module_id/lessons/:lesson_id`
**Descrição:** Atualizar aula

**Headers:** `Authorization: Bearer <token>`

### DELETE `/courses/:course_id/modules/:module_id/lessons/:lesson_id`
**Descrição:** Deletar aula

**Headers:** `Authorization: Bearer <token>`

### POST `/courses/:course_id/enroll`
**Descrição:** Matricular aluno no curso

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "student_id": "uuid",
  "order_id": "uuid"
}
```

### PUT `/courses/:course_id/progress`
**Descrição:** Atualizar progresso da aula

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "lesson_id": "uuid",
  "student_id": "uuid",
  "completed": true,
  "progress_percentage": 100
}
```

### GET `/courses/:course_id/enrollments`
**Descrição:** Listar matrículas do curso

**Headers:** `Authorization: Bearer <token>`

### GET `/courses/enrollment-status/:courseId/:studentEmail`
**Descrição:** Verificar status de matrícula

---

## 🤝 Parceiros (Afiliados/Co-produtores)

### POST `/partners`
**Descrição:** Adicionar parceiro a um produto

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "product_id": "uuid",
  "partner_id": "uuid",
  "partner_type": "co_producer|affiliate",
  "commission_percentage": 30.0
}
```

### GET `/partners/product/:productId`
**Descrição:** Listar parceiros de um produto

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `status`: filtrar por status
- `partner_type`: filtrar por tipo

### GET `/partners/user/:userId`
**Descrição:** Listar parcerias de um usuário

**Headers:** `Authorization: Bearer <token>`

### PUT `/partners/:id`
**Descrição:** Atualizar parceria

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "commission_percentage": 35.0,
  "status": "active|inactive"
}
```

### DELETE `/partners/:id`
**Descrição:** Remover parceria

**Headers:** `Authorization: Bearer <token>`

### GET `/partners/:id/commissions`
**Descrição:** Listar comissões de uma parceria

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `status`: filtrar por status
- `start_date`: data inicial
- `end_date`: data final

### GET `/partners/code/:partnerCode`
**Descrição:** Buscar parceria por código

### POST `/partners/commissions/process`
**Descrição:** Processar comissões de um pedido

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "order_id": "uuid"
}
```

### PUT `/partners/commissions/:id/approve`
**Descrição:** Aprovar comissão

**Headers:** `Authorization: Bearer <token>`

### PUT `/partners/commissions/:id/pay`
**Descrição:** Marcar comissão como paga

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "payment_method": "pix|bank_transfer|wallet",
  "payment_reference": "string"
}
```

### PUT `/partners/commissions/:id/cancel`
**Descrição:** Cancelar comissão

**Headers:** `Authorization: Bearer <token>`

### GET `/partners/commissions/stats/:partnerId`
**Descrição:** Estatísticas de comissões do parceiro

**Headers:** `Authorization: Bearer <token>`

### POST `/partners/validate-code`
**Descrição:** Validar código de parceiro

**Body:**
```json
{
  "partner_code": "string",
  "product_id": "uuid"
}
```

---

## 👥 Usuários

### GET `/users`
**Descrição:** Listar todos os usuários

**Headers:** `Authorization: Bearer <token>`

### GET `/users/:id`
**Descrição:** Buscar usuário específico

**Headers:** `Authorization: Bearer <token>`

### PUT `/users/:id`
**Descrição:** Atualizar dados do usuário

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "name": "string",
  "email": "string",
  "phone": "string",
  "bio": "string"
}
```

### PUT `/users/:id/security`
**Descrição:** Atualizar configurações de segurança

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "current_password": "string",
  "new_password": "string",
  "two_factor_enabled": true
}
```

### PUT `/users/:id/wallet`
**Descrição:** Atualizar saldo da carteira

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "balance": 1000.00
}
```

### GET `/users/:id/stats`
**Descrição:** Estatísticas do usuário

**Headers:** `Authorization: Bearer <token>`

### DELETE `/users/:id`
**Descrição:** Deletar usuário

**Headers:** `Authorization: Bearer <token>`

---

## ⚙️ Configurações

### GET `/settings`
**Descrição:** Buscar todas as configurações do sistema

**Resposta:**
```json
{
  "platformFee": 5,
  "payoutDelayDays": 7,
  "enableNewUserRegistration": true,
  "cardFeePercent": 3.5,
  "transactionFixedFee": 1.0,
  "withdrawalFixedFee": 5.0,
  "pixFeePercent": 1.5,
  "pixFixedFee": 0.50,
  "installmentFees": {
    "2x": 0.5,
    "3x": 0.8,
    "12x": 3.0
  }
}
```

### PUT `/settings`
**Descrição:** Atualizar configurações

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "platformFee": 5,
  "cardFeePercent": 3.5
}
```

### GET `/settings/:key`
**Descrição:** Buscar configuração específica

### DELETE `/settings/:key`
**Descrição:** Deletar configuração específica

**Headers:** `Authorization: Bearer <token>`

---

## 📊 Facebook Tracking

### GET `/facebook/auth/facebook`
**Descrição:** Iniciar autenticação com Facebook

**Headers:** `Authorization: Bearer <token>`

### GET `/facebook/auth/facebook/callback`
**Descrição:** Callback da autenticação do Facebook

### POST `/facebook/config/pixel`
**Descrição:** Configurar Pixel ID

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "pixel_id": "string",
  "access_token": "string",
  "test_event_code": "string"
}
```

### POST `/facebook/track`
**Descrição:** Enviar evento para Facebook Conversion API

**Body:**
```json
{
  "pixel_id": "string",
  "event_name": "Purchase|ViewContent|AddToCart",
  "user_data": {
    "em": "email@example.com",
    "ph": "+5511999999999",
    "fn": "João",
    "ln": "Silva"
  },
  "custom_data": {
    "value": 99.90,
    "currency": "BRL",
    "content_ids": ["product_123"]
  },
  "product_id": "uuid"
}
```

### POST `/facebook/products/:productId/config`
**Descrição:** Configurar Pixel para produto específico

**Headers:** `Authorization: Bearer <token>`

### GET `/facebook/products/:productId/configs`
**Descrição:** Listar configurações de pixel do produto

**Headers:** `Authorization: Bearer <token>`

### DELETE `/facebook/products/:productId/configs/:pixelId`
**Descrição:** Remover configuração de pixel do produto

**Headers:** `Authorization: Bearer <token>`

### POST `/facebook/products/:productId/test-event`
**Descrição:** Testar evento para produto específico

**Headers:** `Authorization: Bearer <token>`

### POST `/facebook/test-event`
**Descrição:** Testar evento do Facebook

**Headers:** `Authorization: Bearer <token>`

### GET `/facebook/status`
**Descrição:** Obter status da configuração do Facebook

**Headers:** `Authorization: Bearer <token>`

### GET `/facebook/logs`
**Descrição:** Obter logs de eventos do Facebook

**Headers:** `Authorization: Bearer <token>`

### POST `/facebook/validate-token`
**Descrição:** Validar Access Token do Facebook

**Headers:** `Authorization: Bearer <token>`

---

## 🎥 Upload de Vídeos

### POST `/upload/video`
**Descrição:** Upload de vídeo com extração de thumbnail

**Content-Type:** `multipart/form-data`

**Form Data:**
- `video`: arquivo de vídeo (MP4, AVI, MOV, WMV, WEBM)

**Query Parameters:**
- `maxSize`: tamanho máximo em MB (padrão: 500)

**Resposta:**
```json
{
  "success": true,
  "message": "Vídeo uploadado com sucesso",
  "video": {
    "filename": "string",
    "originalName": "string",
    "size": 1024000,
    "url": "/uploads/videos/filename.mp4",
    "duration": 300,
    "width": 1920,
    "height": 1080,
    "format": "mp4"
  },
  "thumbnail": {
    "filename": "string",
    "url": "/uploads/thumbnails/thumb_filename.jpg"
  }
}
```

### DELETE `/upload/video/:filename`
**Descrição:** Deletar vídeo e thumbnail

### GET `/upload/info/:filename`
**Descrição:** Obter informações de um vídeo

---

## 🔗 Webhooks

### GET `/webhooks`
**Descrição:** Listar webhooks do usuário

**Headers:** `Authorization: Bearer <token>`

### POST `/webhooks`
**Descrição:** Criar webhook

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "name": "string",
  "url": "https://example.com/webhook",
  "secret": "string",
  "events": ["order.created", "sale.completed"]
}
```

### PUT `/webhooks/:id`
**Descrição:** Atualizar webhook

**Headers:** `Authorization: Bearer <token>`

### DELETE `/webhooks/:id`
**Descrição:** Deletar webhook

**Headers:** `Authorization: Bearer <token>`

### POST `/webhooks/:id/test`
**Descrição:** Testar webhook

**Headers:** `Authorization: Bearer <token>`

### GET `/webhooks/:id/logs`
**Descrição:** Logs do webhook

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page`: número da página
- `limit`: itens por página

### GET `/webhooks/stats`
**Descrição:** Estatísticas gerais de webhooks

**Headers:** `Authorization: Bearer <token>`

### GET `/webhooks/events`
**Descrição:** Listar eventos disponíveis

**Resposta:**
```json
{
  "success": true,
  "events": [
    {
      "value": "order.created",
      "label": "Order Created"
    },
    {
      "value": "sale.completed",
      "label": "Sale Completed"
    }
  ]
}
```

---

## 📧 Email

### POST `/email/send`
**Descrição:** Enviar email

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "to": "email@example.com",
  "subject": "string",
  "html": "<h1>HTML content</h1>",
  "text": "Plain text content"
}
```

### POST `/email/send-template`
**Descrição:** Enviar email com template

**Headers:** `Authorization: Bearer <token>`

**Body:**
```json
{
  "to": "email@example.com",
  "template": "welcome|purchase_confirmation|password_reset",
  "data": {
    "name": "João",
    "product_name": "Curso de JavaScript"
  }
}
```

---

## 📋 Códigos de Status HTTP

- **200 OK**: Requisição bem-sucedida
- **201 Created**: Recurso criado com sucesso
- **400 Bad Request**: Dados inválidos na requisição
- **401 Unauthorized**: Token de autenticação inválido ou ausente
- **403 Forbidden**: Acesso negado
- **404 Not Found**: Recurso não encontrado
- **409 Conflict**: Conflito (ex: email já existe)
- **422 Unprocessable Entity**: Dados válidos mas não processáveis
- **500 Internal Server Error**: Erro interno do servidor

---

## 🔒 Autenticação JWT

A maioria dos endpoints requer autenticação via JWT token no header:

```
Authorization: Bearer <jwt_token>
```

O token é obtido através dos endpoints de login (`/auth/login`) ou registro (`/auth/register`).

---

## 📝 Notas Importantes

1. **Paginação**: Endpoints de listagem suportam paginação via query parameters `page` e `limit`
2. **Filtros**: Muitos endpoints suportam filtros específicos via query parameters
3. **Validação**: Todos os endpoints validam dados de entrada e retornam erros detalhados
4. **Logs**: Sistema mantém logs detalhados de webhooks e eventos do Facebook
5. **Segurança**: Senhas são hasheadas, tokens JWT têm expiração, webhooks suportam assinatura HMAC
6. **Upload**: Sistema suporta upload de vídeos com extração automática de thumbnail
7. **Comissões**: Sistema automatizado de cálculo e processamento de comissões para afiliados
8. **Facebook**: Integração completa com Facebook Conversion API para rastreamento de eventos

---

**Versão da API:** 1.0  
**Última atualização:** Janeiro 2025  
**Desenvolvido por:** CheckoutPro Team
