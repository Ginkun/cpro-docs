# 📚 DOCUMENTAÇÃO COMPLETA

## 🎯 **VISÃO GERAL DO SISTEMA**


## 🏗️ **ARQUITETURA DO SISTEMA**

### **Frontend (React + Vite)**
- **Tecnologia**: React 18+ com Vite
- **UI Framework**: Tailwind CSS + Shadcn/ui
- **Estado**: Context API + LocalStorage
- **Roteamento**: React Router DOM
- **Porta**: 3000 (desenvolvimento)

### **Backend (Node.js)**
- **Tecnologia**: Node.js Express
- **Banco de Dados**: SQLite
- **API**: RESTful com CORS configurado
- **Porta**: 5000 (desenvolvimento)
- **SSL**: Configurável por ambiente

---

## 🚀 **INSTALAÇÃO E CONFIGURAÇÃO**

### **Pré-requisitos**
- Node.js 18+
- npm ou yarn
- Git

### **Início Rápido (Recomendado)**

#### **Windows:**
```bash
./start-dev-local.bat
```

#### **Linux/Mac:**
```bash
chmod +x start-dev-local.sh
./start-dev-local.sh
```

### **Configuração Manual**

#### **1. Backend (Python Flask)**
```bash
cd checkout-backend
pip install -r requirements.txt
python src/main.py
```

#### **2. Frontend (React)**
```bash
npm install
npm run dev
```

### **URLs de Acesso**
- **Frontend**: `http://localhost:3000`
- **Backend API**: `http://localhost:5000/api/status`
- **Admin**: `admin@example.com` / `adminpassword`

---

## 📧 **SISTEMA DE EMAILS**

### **Funcionalidades Implementadas**

#### **1. Verificação de Email no Cadastro**
- Código de 6 dígitos enviado por email
- Validação em tempo real
- Expiração automática (10 minutos)
- Rate limiting por email

#### **2. Recuperação de Senha**
- Link seguro enviado por email
- Token com expiração de 1 hora
- Interface completa de recuperação

#### **3. Emails Automatizados**
- **Boas-vindas**: Enviado após cadastro confirmado
- **Produto Criado**: Notificação para o produtor
- **Nova Venda**: Notificação para o vendedor
- **Confirmação de Compra**: Email para o cliente
- **Carrinho Abandonado**: Email de recuperação automático

### **Configuração SMTP**

Crie um arquivo `.env` na raiz:

```bash
# Gmail SMTP Configuration
VITE_SMTP_HOST=smtp.gmail.com
VITE_SMTP_PORT=587
VITE_SMTP_USER=seu-email@gmail.com
VITE_SMTP_PASS=sua-senha-de-app
VITE_FROM_EMAIL=noreply@checkoutpro.com
VITE_FROM_NAME=CheckoutPro

# Email Service Provider
VITE_EMAIL_PROVIDER=gmail

# Configurações de Funcionalidades
VITE_EMAIL_VERIFICATION_ENABLED=true
VITE_EMAIL_CODE_EXPIRATION=600000
VITE_SEND_WELCOME_EMAIL=true
VITE_SEND_ORDER_CONFIRMATION=true
VITE_SEND_PRODUCT_NOTIFICATION=true
VITE_SEND_RECOVERY_EMAIL=true

# URLs do Sistema
VITE_APP_URL=http://localhost:3000
VITE_API_URL=http://localhost:5000

# Rate Limiting
VITE_EMAIL_RATE_LIMIT=5
VITE_EMAIL_RATE_WINDOW=300000
```

---

## 🛒 **SISTEMA DE CHECKOUT**

### **Modelos de Checkout Disponíveis**

#### **Modelo 1 (Glass Morphism)**
- Design moderno com efeito glass
- Layout responsivo
- Validação em tempo real
- Integração PIX completa

#### **Modelo 2 (Compacto)**
- Interface compacta
- Foco na conversão
- Design minimalista
- Performance otimizada

### **Funcionalidades do Checkout**

#### **1. Validação de Dados**
- Validação de CPF em tempo real
- Verificação de email
- Validação de telefone
- Campos obrigatórios

#### **2. Métodos de Pagamento**
- **PIX**: Integração completa com QR Code
- **Cartão de Crédito**: Processamento seguro
- **Boleto**: Geração automática

#### **3. Taxas e Parcelas**
- Cálculo automático de taxas
- Opções de parcelamento
- Taxas individuais por produto
- Configuração flexível

---

## 💳 **SISTEMA DE PAGAMENTOS**

### **Integração PIX**

#### **Funcionalidades**
- Geração automática de QR Code
- Verificação de pagamento em tempo real
- Webhook para confirmação
- Interface de status do pagamento

#### **Configuração**
```javascript
// Configuração PIX no backend
PIX_KEY=seu-pix-key
PIX_KEY_TYPE=email // ou cpf, cnpj, phone
PIX_MERCHANT_NAME=CheckoutPro
PIX_MERCHANT_CITY=São Paulo
```

### **Webhooks**

#### **Eventos Disponíveis**
- `order.created`: Pedido criado
- `order.updated`: Pedido atualizado
- `sale.completed`: Venda finalizada
- `sale.pending`: Venda pendente
- `test.webhook`: Teste de webhook

#### **Teste de Webhooks**
```bash
# Servidor local
node src/utils/test-webhook-endpoint.js

# URL de teste
http://localhost:8080/webhook
```

---

## 📦 **GESTÃO DE PRODUTOS**

### **Funcionalidades**

#### **1. Criação de Produtos**
- Upload de imagens/vídeos
- Configuração de preços
- Definição de estoque
- Categorização

#### **2. Produtos Digitais**
- Upload de arquivos
- Links de acesso
- Instruções personalizadas
- Entrega automática

#### **3. Produtos Físicos**
- Configuração de frete
- Endereços de entrega
- Rastreamento de pedidos

### **Sistema de Upload**

#### **Configuração**
```javascript
// Configuração de upload
MAX_FILE_SIZE=10MB
ALLOWED_EXTENSIONS=mp4,jpg,png,pdf
UPLOAD_PATH=uploads/
```

---

## 👥 **SISTEMA DE USUÁRIOS**

### **Tipos de Usuário**

#### **1. Administrador**
- Acesso completo ao sistema
- Gestão de usuários
- Configurações da plataforma
- Relatórios e analytics

#### **2. Vendedor**
- Criação de produtos
- Gestão de vendas
- Relatórios de vendas
- Configurações de perfil

#### **3. Cliente**
- Compra de produtos
- Histórico de pedidos
- Gestão de perfil
- Suporte ao cliente

### **Autenticação**

#### **Funcionalidades**
- Registro com verificação de email
- Login seguro
- Recuperação de senha
- 2FA (opcional)

#### **Controle de Registro**
- Configuração para desativar novos registros
- Sistema de notificações para tentativas
- Logs de tentativas de acesso

---

## 📊 **PAINEL ADMINISTRATIVO**

### **Dashboard Principal**

#### **Métricas em Tempo Real**
- Vendas do dia/mês
- Produtos mais vendidos
- Usuários ativos
- Taxa de conversão

#### **Gestão de Pedidos**
- Lista de todos os pedidos
- Filtros por status
- Atualização de status
- Exportação de dados

### **Configurações**

#### **1. Configurações Gerais**
- Nome da plataforma
- Logo e branding
- Configurações de email
- Taxas e comissões

#### **2. Configurações de Pagamento**
- Chaves PIX
- Configurações de cartão
- Webhooks
- Taxas de processamento

#### **3. Configurações de Email**
- Templates personalizados
- Configurações SMTP
- Testes de envio
- Logs de email

---

## 🔒 **SEGURANÇA**

### **Implementações de Segurança**

#### **1. Autenticação**
- Tokens JWT
- Refresh tokens
- Rate limiting
- Validação de sessão

#### **2. Validação de Dados**
- Sanitização de inputs
- Validação de tipos
- Proteção contra XSS
- CSRF protection

#### **3. Criptografia**
- Senhas hasheadas (bcrypt)
- Dados sensíveis criptografados
- SSL/TLS em produção

---

## 📈 **ANALYTICS E RELATÓRIOS**

### **Métricas Disponíveis**

#### **1. Vendas**
- Receita total
- Vendas por período
- Produtos mais vendidos
- Taxa de conversão

#### **2. Usuários**
- Usuários ativos
- Novos registros
- Retenção
- Engajamento

#### **3. Produtos**
- Visualizações
- Conversões
- Estoque
- Performance

---


## 📋 **CHECKLIST DE IMPLEMENTAÇÃO**

### **✅ Funcionalidades Implementadas**

- [x] Sistema de checkout completo
- [x] Integração PIX
- [x] Sistema de emails
- [x] Gestão de produtos
- [x] Painel administrativo
- [x] Sistema de usuários
- [x] Webhooks
- [x] Upload de arquivos
- [x] Analytics básico
- [x] Configurações flexíveis

### **🔄 Funcionalidades em Desenvolvimento**

- [ ] Sistema de WhatsApp
- [ ] Analytics avançado
- [ ] Múltiplos gateways de pagamento
- [ ] Sistema de afiliados
- [ ] API pública
- [ ] App mobile
