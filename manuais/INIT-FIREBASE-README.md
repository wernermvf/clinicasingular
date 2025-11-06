# 🔥 Inicializador do Firebase - Clínica Singular

## 📋 Descrição

Script automatizado para inicializar o Firebase Realtime Database com dados de exemplo completos e estruturados para o sistema Clínica Singular.

## ✨ Recursos

O script cria automaticamente:

- ✅ **3 Usuários** com diferentes níveis de permissão
- ✅ **8 Especialidades** médicas completas
- ✅ **12 Profissionais** com múltiplas especialidades
- ✅ **20 Pacientes** com dados completos, descontos e pacotes
- ✅ **50 Agendamentos** distribuídos nos últimos 6 meses
- ✅ **15 Horários Fixos** recorrentes
- ✅ **100+ Logs de Auditoria** para histórico

## 🚀 Como Usar

### Passo 1: Configurar Firebase

1. Acesse o [Firebase Console](https://console.firebase.google.com/)
2. Selecione seu projeto (ou crie um novo)
3. Vá em **Configurações do Projeto** (ícone de engrenagem)
4. Role até a seção **Seus apps**
5. Copie as credenciais do Firebase Config

### Passo 2: Habilitar Serviços

#### Realtime Database
1. No menu lateral, clique em **Realtime Database**
2. Clique em **Criar banco de dados**
3. Escolha a localização (recomendado: `us-central1`)
4. Selecione **Modo de teste** (ou configure as regras de segurança depois)
5. Clique em **Ativar**

#### Authentication
1. No menu lateral, clique em **Authentication**
2. Clique em **Começar**
3. Clique na aba **Sign-in method**
4. Habilite **E-mail/senha**
5. Salve as alterações

### Passo 3: Executar o Script

1. Abra o arquivo `init-firebase.html` no navegador
2. Preencha os campos com as credenciais do Firebase
3. Marque/desmarque os dados que deseja criar
4. Clique em **Inicializar Banco de Dados**
5. Aguarde a conclusão (acompanhe o progresso no log)

## 🔑 Credenciais de Acesso

Após a inicialização, você poderá acessar o sistema com:

### 👑 Administrator
- **Email**: `admin@clinicasingular.com.br`
- **Senha**: `Admin@123`
- **Permissões**: Acesso total ao sistema

### 👨‍⚕️ Professional
- **Email**: `profissional@clinicasingular.com.br`
- **Senha**: `Prof@123`
- **Permissões**: Gerenciar agendamentos, visualizar pacientes

### 👁️ Viewer
- **Email**: `visualizador@clinicasingular.com.br`
- **Senha**: `View@123`
- **Permissões**: Somente leitura

## 📊 Dados Criados

### Especialidades (8)
- Psicologia
- Fonoaudiologia
- Terapia Ocupacional
- Psicopedagogia
- Nutrição
- Fisioterapia
- Neuropsicologia
- Musicoterapia

### Profissionais (12)
Profissionais com diferentes conselhos profissionais:
- CRP (Psicólogos)
- CRFa (Fonoaudiólogos)
- CREFITO (Fisioterapeutas/Terapeutas Ocupacionais)
- CRN (Nutricionistas)
- MT (Musicoterapeutas)

### Pacientes (20)
Cada paciente possui:
- Dados pessoais completos
- Contato (telefone e email)
- Endereço completo
- 30% têm desconto global (5-25%)
- 25% têm valores customizados por especialidade
- 50% têm pacotes de sessões

### Agendamentos (50)
Distribuídos nos últimos 6 meses com:
- Status realistas (passados já finalizados)
- Valores calculados automaticamente
- Informações de pagamento
- Notas ocasionais

### Horários Fixos (15)
Recorrências semanais de:
- Segunda a sexta-feira
- Horários variados (8h às 17h)
- Vinculados a profissionais e especialidades

## ⚙️ Opções de Inicialização

Você pode escolher quais dados criar:

- ☑️ **Criar Usuários**: Cria as 3 contas de acesso
- ☑️ **Criar Especialidades**: Cria as 8 especialidades médicas
- ☑️ **Criar Profissionais**: Cria os 12 profissionais
- ☑️ **Criar Pacientes**: Cria 20 pacientes com dados variados
- ☑️ **Criar Agendamentos**: Cria 50 agendamentos históricos
- ☑️ **Criar Horários Fixos**: Cria 15 horários recorrentes

## 🗑️ Limpar Banco de Dados

O script também possui a função **Limpar Tudo** que:

- ⚠️ Remove **TODOS** os dados do Firebase
- ⚠️ Ação **IRREVERSÍVEL**
- ⚠️ Requer dupla confirmação

**Use com extremo cuidado!**

## 📝 Regras de Segurança Recomendadas

Após a inicialização, configure as regras de segurança no Firebase:

### Realtime Database Rules

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "auth != null",
        ".write": "auth.uid === $uid || root.child('users').child(auth.uid).child('role').val() === 'administrator'"
      }
    },
    "specialties": {
      ".read": "auth != null",
      ".write": "root.child('users').child(auth.uid).child('role').val() === 'administrator'"
    },
    "professionals": {
      ".read": "auth != null",
      ".write": "root.child('users').child(auth.uid).child('role').val() === 'administrator'"
    },
    "patients": {
      ".read": "auth != null",
      ".write": "root.child('users').child(auth.uid).child('role').val() === 'administrator' || root.child('users').child(auth.uid).child('role').val() === 'professional'"
    },
    "appointments": {
      ".read": "auth != null",
      ".write": "auth != null && root.child('users').child(auth.uid).child('role').val() !== 'viewer'"
    },
    "fixedSchedules": {
      ".read": "auth != null",
      ".write": "root.child('users').child(auth.uid).child('role').val() === 'administrator'"
    },
    "auditLogs": {
      ".read": "root.child('users').child(auth.uid).child('role').val() === 'administrator'",
      ".write": "auth != null"
    }
  }
}
```

## 🔍 Verificação

Após a inicialização, verifique no Firebase Console:

1. **Authentication** → Deve mostrar 3 usuários
2. **Realtime Database** → Deve mostrar 7 coleções
3. Navegue pelas coleções e confirme os dados

## 🐛 Solução de Problemas

### Erro: "Configuração do Firebase incompleta"
- ✅ Verifique se todos os campos foram preenchidos
- ✅ Confirme que copiou as credenciais corretas do Firebase Console

### Erro: "Email already in use"
- ✅ Os usuários já foram criados anteriormente
- ✅ Use a função "Limpar Tudo" antes de recriar

### Erro: "Permission denied"
- ✅ Verifique se o Realtime Database está em modo de teste
- ✅ Ou configure as regras de segurança adequadamente

### Erro: "Network error"
- ✅ Verifique sua conexão com a internet
- ✅ Confirme que o Firebase está ativo e acessível

## 📈 Próximos Passos

Após a inicialização:

1. ✅ Acesse o `index.html` do sistema
2. ✅ Faça login com uma das contas criadas
3. ✅ Explore o Dashboard com dados reais
4. ✅ Teste os relatórios PDF
5. ✅ Experimente criar novos registros
6. ✅ Verifique os logs de auditoria

## 🎯 Dicas de Uso

### Para Demonstrações
- Use a conta **Administrator** para mostrar todas as funcionalidades
- Os dados criados são realistas e profissionais
- O Dashboard já mostrará gráficos e estatísticas

### Para Desenvolvimento
- Use a conta **Professional** para testar permissões
- Crie novos agendamentos para testar conflitos
- Experimente o sistema de pacotes

### Para Testes
- Use a função "Limpar Tudo" para recomeçar
- Crie dados específicos desmarcando certas opções
- Teste diferentes cenários de uso

## 📊 Estrutura dos Dados

### Appointments
```javascript
{
  date: "2024-11-06",
  startTime: "14:00",
  endTime: "14:50",
  duration: 50,
  patientId: "patient-id",
  professionalId: "professional-id",
  specialtyId: "specialty-id",
  status: "present",
  financial: {
    patientValue: 180.00,
    professionalValue: 60.00,
    isPaid: true
  },
  notes: "...",
  createdAt: "2024-11-01T10:00:00Z"
}
```

### Patients
```javascript
{
  name: "João Silva",
  birthDate: "1995-05-15",
  phone: "(11) 98765-4321",
  email: "joao.silva@email.com",
  address: "Rua das Flores, 123",
  neighborhood: "Centro",
  city: "São Paulo",
  state: "SP",
  zipCode: "01234-567",
  discount: 10,
  customValues: {
    "specialty-id": 150.00
  },
  packages: {
    "package-id": {
      specialtyId: "specialty-id",
      name: "Pacote 8 Sessões",
      totalSessions: 8,
      usedSessions: 3,
      price: 1200.00,
      createdAt: "2024-10-01T10:00:00Z"
    }
  },
  active: true,
  createdAt: "2024-09-01T10:00:00Z"
}
```

### Fixed Schedules
```javascript
{
  professionalId: "professional-id",
  specialtyId: "specialty-id",
  dayOfWeek: 1, // 0=Dom, 1=Seg, 2=Ter...
  startTime: "14:00",
  duration: 50,
  active: true,
  createdAt: "2024-11-01T10:00:00Z"
}
```

## 🔐 Segurança

### Recomendações
- ✅ Altere as senhas padrão após o primeiro login
- ✅ Configure regras de segurança no Firebase
- ✅ Habilite autenticação de dois fatores
- ✅ Faça backups regulares dos dados
- ✅ Monitore os logs de auditoria

### Dados Sensíveis
- ⚠️ Os dados criados são fictícios
- ⚠️ Não use em produção sem ajustes
- ⚠️ CPF, RG e outros documentos devem ser adicionados conforme necessário

## 📞 Suporte

Se encontrar problemas:

1. Verifique o console do navegador (F12)
2. Confirme a configuração do Firebase
3. Revise as permissões do Realtime Database
4. Consulte a documentação do Firebase

## 📄 Licença

Este script faz parte do sistema Clínica Singular.

---

**Versão**: 1.0  
**Data**: Novembro 2024  
**Autor**: Sistema Clínica Singular  

🎉 **Pronto para inicializar seu banco de dados!**
