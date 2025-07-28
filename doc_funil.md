# 📊 Sistema de Funil - Documentação Completa

## 🎯 Visão Geral

Este documento descreve a implementação completa do sistema de funil de vendas no CheckoutPro, incluindo upsells, downsells e analytics avançados.

## 🗄️ Estrutura do Banco de Dados

### Tabelas Criadas

#### 1. **order_items** - Itens do Pedido
Armazena todos os produtos/serviços incluídos em um pedido.

```sql
CREATE TABLE order_items (
  id SERIAL PRIMARY KEY,
  order_id VARCHAR NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id VARCHAR NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  type VARCHAR(50) NOT NULL DEFAULT 'main_product',
  price DECIMAL(10,2) NOT NULL,
  quantity INTEGER NOT NULL DEFAULT 1,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Campos:**
- `id`: Identificador único do item
- `order_id`: Referência ao pedido
- `product_id`: Referência ao produto
- `type`: Tipo do item (`main_product`, `upsell`, `downsell`, `bonus`)
- `price`: Preço do item no momento da compra
- `quantity`: Quantidade do item

#### 2. **product_upsells** - Configuração de Upsells
Define os produtos de upsell/downsell para cada produto principal.

```sql
CREATE TABLE product_upsells (
  id SERIAL PRIMARY KEY,
  main_product_id VARCHAR NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  upsell_product_id VARCHAR NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  type VARCHAR(50) NOT NULL DEFAULT 'upsell',
  position INTEGER NOT NULL DEFAULT 1,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Campos:**
- `id`: Identificador único
- `main_product_id`: Produto principal
- `upsell_product_id`: Produto de upsell/downsell
- `type`: Tipo (`upsell`, `downsell`, `cross_sell`)
- `position`: Ordem de apresentação
- `is_active`: Se está ativo

#### 3. **funnel_analytics** - Analytics do Funil
Registra todos os eventos e interações no funil de vendas.

```sql
CREATE TABLE funnel_analytics (
  id SERIAL PRIMARY KEY,
  order_id VARCHAR NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id VARCHAR REFERENCES products(id) ON DELETE SET NULL,
  upsell_id INTEGER REFERENCES product_upsells(id) ON DELETE SET NULL,
  event_type VARCHAR(100) NOT NULL,
  funnel_step VARCHAR(50) NOT NULL,
  user_id VARCHAR REFERENCES users(id) ON DELETE SET NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Campos:**
- `id`: Identificador único
- `order_id`: Referência ao pedido
- `product_id`: Produto relacionado ao evento
- `upsell_id`: Upsell relacionado (se aplicável)
- `event_type`: Tipo do evento (`view`, `accept`, `decline`, `timeout`)
- `funnel_step`: Etapa do funil (`main_product`, `upsell_1`, `upsell_2`, `downsell`, `thank_you`)
- `user_id`: Usuário que realizou a ação

### Campos Adicionados à Tabela `orders`

```sql
-- Novos campos adicionados
ALTER TABLE orders ADD COLUMN seller_id VARCHAR REFERENCES users(id) ON DELETE SET NULL;
ALTER TABLE orders ADD COLUMN total_amount DECIMAL(10,2);
ALTER TABLE orders ADD COLUMN payment_status VARCHAR(50) DEFAULT 'pending';
ALTER TABLE orders ADD COLUMN transaction_id VARCHAR(255);
ALTER TABLE orders ADD COLUMN next_step VARCHAR(100);
ALTER TABLE orders ADD COLUMN funnel_step VARCHAR(50) DEFAULT 'main_product';
ALTER TABLE orders ADD COLUMN expires_at TIMESTAMP;
```

**Novos Campos:**
- `seller_id`: Vendedor responsável pelo pedido
- `total_amount`: Valor total final do pedido (incluindo upsells)
- `payment_status`: Status do pagamento (`pending`, `paid`, `failed`, `refunded`)
- `transaction_id`: ID da transação no gateway de pagamento
- `next_step`: Próxima etapa no funil
- `funnel_step`: Etapa atual no funil
- `expires_at`: Data de expiração da oferta

## 🔍 Índices Criados

Para otimizar as consultas, foram criados os seguintes índices:

```sql
-- Índices na tabela orders
CREATE INDEX idx_orders_seller_id ON orders(seller_id);
CREATE INDEX idx_orders_payment_status ON orders(payment_status);
CREATE INDEX idx_orders_funnel_step ON orders(funnel_step);

-- Índices na tabela order_items
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
CREATE INDEX idx_order_items_type ON order_items(type);

-- Índices na tabela product_upsells
CREATE INDEX idx_product_upsells_main_product_id ON product_upsells(main_product_id);
CREATE INDEX idx_product_upsells_type ON product_upsells(type);

-- Índices na tabela funnel_analytics
CREATE INDEX idx_funnel_analytics_order_id ON funnel_analytics(order_id);
CREATE INDEX idx_funnel_analytics_event_type ON funnel_analytics(event_type);
CREATE INDEX idx_funnel_analytics_funnel_step ON funnel_analytics(funnel_step);
```

## 🚀 Endpoints da API

### 1. Configuração de Upsells

#### `POST /api/products/:productId/upsells`
Cria uma configuração de upsell para um produto.

**Parâmetros:**
```json
{
  "upsell_product_id": "string",
  "type": "upsell|downsell|cross_sell",
  "position": "number",
  "is_active": "boolean"
}
```

#### `GET /api/products/:productId/upsells`
Retorna todos os upsells configurados para um produto.

#### `PUT /api/products/:productId/upsells/:upsellId`
Atualiza uma configuração de upsell.

#### `DELETE /api/products/:productId/upsells/:upsellId`
Remove uma configuração de upsell.

### 2. Processamento do Funil

#### `POST /api/funnel/start`
Inicia um novo funil de vendas.

**Parâmetros:**
```json
{
  "product_id": "string",
  "customer_info": {
    "name": "string",
    "email": "string",
    "phone": "string"
  },
  "utm_params": {
    "source": "string",
    "medium": "string",
    "campaign": "string"
  }
}
```

**Resposta:**
```json
{
  "order_id": "string",
  "funnel_step": "main_product",
  "next_step": "upsell_1",
  "expires_at": "2024-12-23T10:00:00Z"
}
```

#### `POST /api/funnel/:orderId/accept-upsell`
Aceita um upsell no funil.

**Parâmetros:**
```json
{
  "upsell_id": "number",
  "quantity": "number"
}
```

#### `POST /api/funnel/:orderId/decline-upsell`
Rejeita um upsell no funil.

**Parâmetros:**
```json
{
  "upsell_id": "number",
  "reason": "string"
}
```

#### `GET /api/funnel/:orderId/next-step`
Retorna a próxima etapa do funil.

**Resposta:**
```json
{
  "current_step": "upsell_1",
  "next_step": "upsell_2",
  "upsell_product": {
    "id": "string",
    "name": "string",
    "price": "number",
    "description": "string"
  },
  "expires_at": "2024-12-23T10:00:00Z"
}
```

### 3. Analytics do Funil

#### `GET /api/analytics/funnel/:orderId`
Retorna analytics detalhados de um pedido específico.

**Resposta:**
```json
{
  "order_id": "string",
  "total_events": "number",
  "conversion_rate": "number",
  "steps": [
    {
      "step": "main_product",
      "events": [
        {
          "event_type": "view",
          "timestamp": "2024-12-23T10:00:00Z"
        }
      ]
    }
  ]
}
```

#### `GET /api/analytics/funnel/product/:productId`
Retorna analytics de funil para um produto específico.

**Parâmetros de Query:**
- `start_date`: Data de início (YYYY-MM-DD)
- `end_date`: Data de fim (YYYY-MM-DD)
- `step`: Filtrar por etapa específica

**Resposta:**
```json
{
  "product_id": "string",
  "period": {
    "start_date": "2024-12-01",
    "end_date": "2024-12-23"
  },
  "metrics": {
    "total_orders": "number",
    "conversion_rate": "number",
    "average_order_value": "number",
    "upsell_acceptance_rate": "number"
  },
  "funnel_steps": [
    {
      "step": "main_product",
      "views": "number",
      "conversions": "number",
      "conversion_rate": "number"
    }
  ]
}
```

#### `GET /api/analytics/funnel/overview`
Retorna visão geral dos analytics do funil.

**Resposta:**
```json
{
  "total_funnels": "number",
  "active_funnels": "number",
  "completed_funnels": "number",
  "average_completion_time": "number",
  "top_performing_products": [
    {
      "product_id": "string",
      "product_name": "string",
      "conversion_rate": "number",
      "total_revenue": "number"
    }
  ]
}
```

### 4. Relatórios

#### `GET /api/reports/funnel-performance`
Gera relatório de performance do funil.

**Parâmetros de Query:**
- `period`: `daily|weekly|monthly`
- `start_date`: Data de início
- `end_date`: Data de fim
- `product_id`: Filtrar por produto (opcional)

#### `GET /api/reports/upsell-performance`
Gera relatório de performance dos upsells.

#### `GET /api/reports/conversion-funnel`
Gera relatório de conversão por etapa do funil.

## 🔧 Configuração e Uso

### 1. Configurando Upsells

```javascript
// Exemplo: Configurar upsell para um produto
const upsellConfig = {
  main_product_id: 'produto-principal-123',
  upsell_product_id: 'upsell-produto-456',
  type: 'upsell',
  position: 1,
  is_active: true
};

// POST /api/products/produto-principal-123/upsells
```

### 2. Iniciando um Funil

```javascript
// Exemplo: Iniciar funil de vendas
const funnelStart = {
  product_id: 'produto-principal-123',
  customer_info: {
    name: 'João Silva',
    email: 'joao@email.com',
    phone: '11999999999'
  },
  utm_params: {
    source: 'facebook',
    medium: 'cpc',
    campaign: 'black-friday'
  }
};

// POST /api/funnel/start
```

### 3. Processando Upsells

```javascript
// Aceitar upsell
const acceptUpsell = {
  upsell_id: 1,
  quantity: 1
};

// POST /api/funnel/order-123/accept-upsell

// Rejeitar upsell
const declineUpsell = {
  upsell_id: 1,
  reason: 'Preço muito alto'
};

// POST /api/funnel/order-123/decline-upsell
```
