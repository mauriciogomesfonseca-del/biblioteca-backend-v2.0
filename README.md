# Ala dos Estudantes — Paiva Netto
## Backend API — Guia de Deploy

---

## ✅ Pré-requisitos
- Conta no [GitHub](https://github.com)
- Conta no [Supabase](https://supabase.com)
- Conta na [Vercel](https://vercel.com)
- Node.js 18+ instalado localmente

---

## 📦 Passo a Passo Completo

### 1. Criar o banco no Supabase

1. Acesse [supabase.com](https://supabase.com) → **New Project**
2. Nome: `biblioteca-paiva-netto` | Região: South America (São Paulo)
3. Aguarde criar (~2 min)
4. Vá em **SQL Editor** → **New Query**
5. Cole o conteúdo de `sql/schema.sql` e clique **Run**
6. Vá em **Project Settings → API** e copie:
   - `Project URL` → `SUPABASE_URL`
   - `service_role` secret → `SUPABASE_SERVICE_ROLE_KEY`

### 2. Subir o código no GitHub

```bash
git init
git add .
git commit -m "feat: backend inicial da Ala dos Estudantes"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/biblioteca-paiva-netto.git
git push -u origin main
```

### 3. Gerar as senhas com bcrypt

```bash
npm install
npm run gerar-hash -- SUA_SENHA_RECEPCAO
npm run gerar-hash -- SUA_SENHA_ADMIN
```
Copie os dois hashes gerados.

### 4. Gerar JWT_SECRET e CRON_SECRET

```bash
node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### 5. Deploy na Vercel

1. Acesse [vercel.com](https://vercel.com) → **Add New Project**
2. Importe o repositório do GitHub
3. **Framework Preset:** Other
4. Clique em **Environment Variables** e adicione:

| Variável | Valor |
|---|---|
| `SUPABASE_URL` | URL copiada do Supabase |
| `SUPABASE_SERVICE_ROLE_KEY` | service_role key |
| `JWT_SECRET` | hash gerado no passo 4 |
| `SENHA_RECEPCAO_HASH` | hash bcrypt da senha de recepção |
| `SENHA_ADMIN_HASH` | hash bcrypt da senha admin |
| `CRON_SECRET` | secret gerado no passo 4 |

5. Clique **Deploy**

### 6. Testar os endpoints

Substitua `SUA_URL` pela URL gerada pela Vercel (ex: `https://biblioteca-paiva-netto.vercel.app`)

```bash
# Verificar vagas (público)
curl https://SUA_URL/api/vagas/status

# Login recepção
curl -X POST https://SUA_URL/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"senha":"suasenha","tipo":"recepcao"}'

# O token retornado use como Bearer nas próximas chamadas
TOKEN="cole_o_token_aqui"

# Listar usuários
curl https://SUA_URL/api/usuarios/listar \
  -H "Authorization: Bearer $TOKEN"

# Cadastrar usuário
curl -X POST https://SUA_URL/api/usuarios/cadastrar \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"nome":"João Silva","cpf":"12345678900","tipo":"aluno"}'
```

---

## 🗺️ Endpoints disponíveis

| Método | Endpoint | Auth | Descrição |
|---|---|---|---|
| GET | `/api/vagas/status` | ❌ público | Vagas livres/ocupadas |
| POST | `/api/auth/login` | ❌ público | Login retorna JWT |
| GET | `/api/usuarios/listar` | ✅ | Lista/busca usuários |
| POST | `/api/usuarios/cadastrar` | ✅ | Cadastra novo usuário |
| GET | `/api/acessos/listar` | ✅ | Acessos do dia |
| POST | `/api/acessos/registrar` | ✅ | Registra entrada |
| POST | `/api/acessos/encerrar-dia` | ✅ admin | Encerra todos os acessos |
| POST | `/api/pagamentos/webhook` | 🔑 cron | Webhook do gateway Pix |

---

## 🔮 Próximos passos

- **Passo 2:** Integração do frontend HTML com esta API (substituir localStorage)
- **Passo 3:** Gateway Pix — Mercado Pago ou EfiBank
- **Passo 4:** Control iD — liberar catraca via webhook
- **Passo 5:** Supabase Storage — upload de fotos
