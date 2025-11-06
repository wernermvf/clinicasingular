# 🚀 Início Rápido - Sistema Clínica Singular

## 📋 Pré-requisitos
- Conta no Firebase (gratuita)
- Navegador moderno (Chrome, Firefox, Edge, Safari)
- Conexão com internet

---

## ⚡ Setup Rápido (15 minutos)

### 1️⃣ Configurar Firebase (5 min)

#### Criar Projeto
1. Acessar https://console.firebase.google.com
2. Clicar em "Adicionar projeto"
3. Nome: "Clínica Singular" (ou nome de sua escolha)
4. Desabilitar Google Analytics (opcional)
5. Criar projeto

#### Ativar Authentication
1. No menu lateral: **Authentication**
2. Clicar em "Começar"
3. Aba "Sign-in method"
4. Ativar "Email/Password"
5. Salvar

#### Ativar Realtime Database
1. No menu lateral: **Realtime Database**
2. Clicar em "Criar banco de dados"
3. Localização: Estados Unidos (us-central1)
4. Modo: **Teste** (vamos alterar depois)
5. Ativar

#### Copiar Configuração
1. Menu lateral: **Configurações do projeto** (ícone engrenagem)
2. Rolar até "Seus aplicativos"
3. Clicar no ícone **Web** (`</>`)
4. Apelido: "Web App"
5. **Copiar o objeto `firebaseConfig`**

### 2️⃣ Atualizar index.html (2 min)

Abrir `index.html` e localizar (linha ~838):

```javascript
const firebaseConfig = {
    apiKey: "AIzaSy...",
    authDomain: "...",
    databaseURL: "...",
    // ...
};
```

**Substituir** pelos valores copiados do Firebase.

### 3️⃣ Criar Usuários (5 min)

#### No Firebase Console
1. Authentication > Users
2. Add user:
   - **Admin**: admin@clinica.com / senha123
   - **Recepção**: recepcao@clinica.com / senha123
   - **Psicólogo**: psicologo@clinica.com / senha123
3. **Copiar os UIDs** de cada usuário

#### No Realtime Database
1. Realtime Database > Data
2. Clicar no `+` ao lado da URL
3. Nome: `users`
4. Adicionar estrutura:

```json
{
  "users": {
    "UID-DO-ADMIN-AQUI": {
      "email": "admin@clinica.com",
      "name": "Administrador",
      "role": "administrator",
      "status": "active"
    },
    "UID-DA-RECEPCAO-AQUI": {
      "email": "recepcao@clinica.com",
      "name": "Recepcionista",
      "role": "reception",
      "status": "active"
    },
    "UID-DO-PSICOLOGO-AQUI": {
      "email": "psicologo@clinica.com",
      "name": "Dr. João Silva",
      "role": "therapist",
      "status": "active"
    }
  }
}
```

**⚠️ IMPORTANTE**: Substituir os UIDs pelos valores reais!

### 4️⃣ Configurar Regras de Segurança (3 min)

1. Realtime Database > Rules
2. Copiar e colar:

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "auth != null",
        ".write": "auth != null && auth.uid === $uid"
      }
    },
    "professionals": {
      ".read": "auth != null",
      "$id": {
        ".write": "auth != null"
      }
    },
    "specialties": {
      ".read": "auth != null",
      "$id": {
        ".write": "auth != null"
      }
    },
    "patients": {
      ".read": "auth != null",
      "$id": {
        ".write": "auth != null"
      }
    },
    "appointments": {
      ".read": "auth != null",
      "$id": {
        ".write": "auth != null"
      }
    },
    "auditLog": {
      ".read": "auth != null",
      ".write": "auth != null"
    }
  }
}
```

3. Publicar

---

## 🎯 Primeiro Uso

### 1. Abrir o Sistema
- Abrir `index.html` em um navegador
- Ou hospedar em servidor local (ex: Live Server do VS Code)

### 2. Login
- Email: admin@clinica.com
- Senha: senha123

### 3. Criar Primeira Especialidade
1. Menu lateral > **Especialidades**
2. Botão "Nova Especialidade"
3. Preencher:
   - Nome: Psicologia
   - Descrição: Atendimento psicológico
   - Cor: #3B82F6 (azul)
   - Duração: 50 minutos
   - Valor: R$ 150,00
4. Salvar

### 4. Criar Primeiro Profissional
1. Menu lateral > **Profissionais**
2. Botão "Novo Profissional"
3. Preencher:
   - Nome: Dra. Maria Silva
   - Email: maria@clinica.com
   - Telefone: (11) 98765-4321
4. Marcar especialidade "Psicologia"
5. Configurar repasse: 70% (percentual)
6. Salvar

### 5. Criar Primeiro Paciente
**Nota**: Modal de paciente ainda não implementado
Por enquanto, adicionar manualmente no Firebase:

```json
{
  "patients": {
    "pac-001": {
      "name": "João da Silva",
      "birthDate": "2010-05-15",
      "responsible": {
        "name": "Maria da Silva",
        "phone": "(11) 91234-5678",
        "email": "maria@email.com"
      },
      "status": "active",
      "globalDiscount": 0
    }
  }
}
```

### 6. Criar Primeiro Agendamento
**Nota**: Modal de agendamento ainda não implementado
Adicionar manualmente no Firebase:

```json
{
  "appointments": {
    "app-001": {
      "patientId": "pac-001",
      "professionalId": "COPIAR-ID-DO-PROFISSIONAL",
      "specialtyId": "COPIAR-ID-DA-ESPECIALIDADE",
      "date": "2024-01-25",
      "startTime": "10:00",
      "endTime": "10:50",
      "status": "scheduled"
    }
  }
}
```

### 7. Registrar Atendimento
1. Menu lateral > **Atendimentos**
2. Localizar agendamento de João da Silva
3. Clicar em "Presente"
4. Verificar preview financeiro:
   - Paciente: R$ 150,00
   - Profissional: R$ 105,00
5. Adicionar observações (opcional)
6. Salvar

### 8. Verificar Auditoria
1. Menu lateral > **Auditoria**
2. Ver histórico de todas as operações

---

## 🔧 Funcionalidades Disponíveis

### ✅ Totalmente Funcionais
- Login/Logout
- CRUD Especialidades
- CRUD Profissionais
- Atualização de status de atendimentos
- Cálculo automático de valores
- Auditoria completa
- Visualização de agenda de profissionais
- Sistema de permissões por role

### 🔄 Parcialmente Funcionais
- Offline sync (estrutura existe, sincronização incompleta)
- Dashboard (layout pronto, gráficos estáticos)
- Relatórios (página criada, PDF não implementado)

### ❌ Pendentes
- CRUD Pacientes (modal completo)
- CRUD Agendamentos
- CRUD Horários fixos
- Geração automática de recorrências
- Relatórios em PDF
- Dashboard com dados reais

---

## 👥 Perfis de Usuário

### Administrator (Administrador)
**Acesso Total**
- Dashboard com métricas
- Cadastros (profissionais, especialidades, pacientes)
- Atendimentos
- Financeiro
- Relatórios
- Auditoria
- Configurações
- Usuários

### Reception (Recepcionista)
**Operacional**
- Agenda
- Cadastros (profissionais, pacientes)
- Atendimentos (registrar status)
- Relatórios

### Therapist (Profissional)
**Somente Leitura**
- Minha Agenda (visualização apenas)

---

## 💡 Dicas Rápidas

### Atalhos de Navegação
- `#/dashboard` - Painel principal (Admin)
- `#/profissionais` - Profissionais
- `#/especialidades` - Especialidades
- `#/atendimentos` - Atendimentos
- `#/auditoria` - Auditoria (Admin)

### Ver Dados no Firebase
- Firebase Console > Realtime Database > Data
- Expandir nós para ver estrutura
- Clicar em `+` para adicionar
- Clicar em `✏️` para editar
- Clicar em `🗑️` para deletar

### Verificar Auditoria
Toda operação CRUD gera log em `/auditLog`:
- Timestamp
- Usuário
- Ação
- Entidade
- Mudanças detalhadas

### Cálculos Financeiros
O sistema calcula automaticamente:
1. Busca valor da especialidade
2. Aplica valor customizado (se houver)
3. Aplica pacote ativo (se houver)
4. Aplica desconto global
5. Calcula repasse ao profissional

### Console do Navegador
Abrir DevTools (F12) > Console para:
- Ver mensagens de log
- Verificar erros
- Testar funções manualmente

---

## 🐛 Troubleshooting

### Erro: "Permission denied"
- Verificar regras de segurança no Firebase
- Verificar se usuário tem perfil em `/users`
- Verificar se role está correto

### Erro: "Firebase not initialized"
- Verificar se `firebaseConfig` está correto
- Verificar se Firebase SDK está carregando (conexão internet)

### Botões não funcionam
- Abrir console (F12) e verificar erros
- Verificar se `setupPageEventListeners()` foi chamado
- Recarregar página (Ctrl+F5)

### Modal não abre
- Verificar console para erros
- Verificar se dados existem no Firebase
- Tentar criar novo registro

### Valores não calculam
- Verificar se especialidade tem `defaultValue`
- Verificar se profissional tem configuração de repasse
- Ver console para erros

---

## 📚 Documentação Completa

Para detalhes técnicos completos, consultar:
- `ESTRUTURA-DADOS.md` - Modelo de dados
- `PROGRESSO-DESENVOLVIMENTO.md` - Status do projeto
- `TESTES-FUNCIONALIDADES.md` - Testes detalhados
- `RESUMO-IMPLEMENTACAO.md` - Implementação técnica
- `CHECKLIST-VERIFICACAO.md` - Checklist completo
- `TROUBLESHOOTING.md` - Solução de problemas

---

## 🎓 Próximos Passos

Após setup inicial:

1. **Cadastrar dados reais**
   - Especialidades da clínica
   - Profissionais
   - Pacientes
   - Horários fixos

2. **Testar fluxo completo**
   - Criar agendamento
   - Registrar presença
   - Verificar cálculos
   - Gerar relatório

3. **Customizar**
   - Alterar cores/logo
   - Ajustar valores padrão
   - Configurar regras de negócio

4. **Implementar pendências**
   - Modal de pacientes
   - Modal de agendamentos
   - Relatórios PDF

---

## 💬 Suporte

Para dúvidas:
1. Consultar documentação acima
2. Verificar console do navegador (F12)
3. Consultar logs de auditoria
4. Verificar Firebase Console

---

**Sistema pronto para uso! 🚀**

Versão: 1.0  
Última atualização: Janeiro 2024  
Status: 85% funcional
