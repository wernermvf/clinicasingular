# Estrutura de Dados - Sistema Clínica Singular

## 📊 Modelo de Dados no Firebase Realtime Database

### 1. **users/** (Usuários do Sistema)
```json
users/
  {uid}/
    email: string
    name: string
    role: "administrator" | "reception" | "therapist"
    status: "active" | "inactive"
    professionalId: string | null  // Se for therapist, link para professionals
    createdAt: timestamp
    updatedAt: timestamp
```

**Roles e Permissões:**
- `administrator`: Acesso total, cadastros, relatórios, auditoria, edição global
- `reception`: Registrar status atendimentos, gerenciar agendas, cadastros, relatórios mensais
- `therapist`: Apenas visualizar própria agenda e resumo de atendimentos (sem edição)

---

### 2. **specialties/** (Especialidades)
```json
specialties/
  {specialtyId}/
    name: string              // Ex: "Psicologia", "Fonoaudiologia"
    description: string
    defaultValue: number      // Valor padrão da consulta
    active: boolean
    createdAt: timestamp
    updatedAt: timestamp
    createdBy: uid
```

---

### 3. **professionals/** (Profissionais)
```json
professionals/
  {professionalId}/
    name: string
    email: string
    phone: string
    userId: uid | null         // Link com user se tiver acesso ao sistema
    specialties: {
      {specialtyId}: {
        repassPercentage: number   // Percentual de repasse (0-100)
        repassFixedValue: number | null  // Ou valor fixo de repasse
      }
    }
    availableHours: {
      monday: [{start: "08:00", end: "12:00"}, {start: "14:00", end: "18:00"}],
      tuesday: [...],
      wednesday: [...],
      thursday: [...],
      friday: [...],
      saturday: [...],
      sunday: [...]
    }
    active: boolean
    createdAt: timestamp
    updatedAt: timestamp
    createdBy: uid
```

---

### 4. **patients/** (Pacientes)
```json
patients/
  {patientId}/
    name: string
    birthDate: string
    cpf: string | null
    phone: string
    email: string | null
    address: {
      street: string
      number: string
      complement: string
      neighborhood: string
      city: string
      state: string
      zipCode: string
    }
    responsibleName: string | null     // Responsável financeiro
    responsiblePhone: string | null
    responsibleCPF: string | null
    
    // Valores personalizados por especialidade
    customValues: {
      {specialtyId}: {
        value: number
        notes: string
      }
    }
    
    // Pacotes e descontos
    packages: {
      {packageId}: {
        specialtyId: string
        totalSessions: number
        usedSessions: number
        valuePerSession: number
        expiresAt: timestamp | null
        active: boolean
      }
    }
    
    globalDiscount: number | null     // Desconto percentual global
    
    status: "active" | "inactive"
    notes: string
    createdAt: timestamp
    updatedAt: timestamp
    createdBy: uid
```

---

### 5. **fixedSchedules/** (Agendas Fixas Semanais)
```json
fixedSchedules/
  {scheduleId}/
    patientId: string
    professionalId: string
    specialtyId: string
    dayOfWeek: 0-6            // 0=Domingo, 1=Segunda, etc
    startTime: string         // "14:00"
    endTime: string           // "15:00"
    startDate: string         // Data de início da recorrência
    endDate: string | null    // Data de fim (null = indefinido)
    active: boolean
    createdAt: timestamp
    updatedAt: timestamp
    createdBy: uid
```

---

### 6. **appointments/** (Atendimentos)
```json
appointments/
  {appointmentId}/
    patientId: string
    professionalId: string
    specialtyId: string
    date: string              // "2025-11-05"
    startTime: string         // "14:00"
    endTime: string           // "15:00"
    
    // Origem do atendimento
    source: "manual" | "fixed_schedule"
    fixedScheduleId: string | null
    
    // Status e valores
    status: "scheduled" | "present" | "absent" | "cancelled"
    statusUpdatedAt: timestamp | null
    statusUpdatedBy: uid | null
    
    // Cálculos financeiros (preenchidos automaticamente ao marcar status)
    financial: {
      patientValue: number      // Valor a cobrar do paciente
      professionalValue: number  // Valor de repasse ao profissional
      usedPackage: boolean      // Se usou sessão de pacote
      packageId: string | null
      discount: number | null   // Desconto aplicado
      notes: string
    }
    
    // Informações adicionais
    notes: string
    cancellationReason: string | null
    hasMedicalCertificate: boolean  // Se teve atestado
    
    createdAt: timestamp
    updatedAt: timestamp
    createdBy: uid
```

**Lógica de Status:**
- `scheduled`: Agendado (sem valores calculados)
- `present`: Paciente compareceu → Gerar valores para cobrança
- `absent`: Faltou sem avisar → Gerar valores para cobrança
- `cancelled`: Cancelado com aviso/atestado → **NÃO** gerar valores

---

### 7. **scheduleChanges/** (Histórico de Alterações em Agendas Fixas)
```json
scheduleChanges/
  {changeId}/
    fixedScheduleId: string
    changeType: "created" | "updated" | "deleted" | "suspended"
    date: string              // Data específica da alteração
    isPermanent: boolean      // Se altera toda a recorrência ou só uma data
    
    previousData: object | null
    newData: object
    
    reason: string
    createdAt: timestamp
    createdBy: uid
```

---

### 8. **monthlyReports/** (Relatórios Mensais Gerados)
```json
monthlyReports/
  {year}/
    {month}/
      patients/
        {patientId}/
          generated: boolean
          generatedAt: timestamp
          generatedBy: uid
          appointments: [
            {
              date: string
              professional: string
              specialty: string
              status: string
              value: number
            }
          ]
          totalValue: number
          pdfUrl: string | null
      
      professionals/
        {professionalId}/
          generated: boolean
          generatedAt: timestamp
          generatedBy: uid
          summary: {
            totalPresent: number
            totalAbsent: number
            totalCancelled: number
            totalValue: number
          }
          appointments: [...]
          pdfUrl: string | null
```

---

### 9. **auditLog/** (Registro de Auditoria)
```json
auditLog/
  {logId}/
    timestamp: timestamp
    userId: uid
    userName: string
    userRole: string
    action: string            // "create", "update", "delete", "status_change"
    entityType: string        // "patient", "professional", "appointment", etc
    entityId: string
    
    changes: {
      field: string
      oldValue: any
      newValue: any
    }[]
    
    ipAddress: string | null
    userAgent: string | null
```

---

### 10. **systemSettings/** (Configurações do Sistema)
```json
systemSettings/
  clinic/
    name: string
    cnpj: string
    phone: string
    email: string
    address: object
    logo: string | null
  
  financial/
    defaultRepassPercentage: number
    allowNegativeBalance: boolean
    autoGenerateReports: boolean
    reportGenerationDay: number  // Dia do mês (1-31)
  
  schedule/
    minAdvanceBooking: number    // Horas mínimas de antecedência
    maxAdvanceBooking: number    // Dias máximos no futuro
    defaultSessionDuration: number  // Minutos
    allowConflicts: boolean
    workingDays: [1, 2, 3, 4, 5]  // Dias da semana que funciona
```

---

## 🔄 Fluxo de Dados Principais

### Fluxo 1: Criação de Agenda Fixa
1. Recepção cria agenda fixa semanal
2. Sistema valida conflitos de horário
3. Sistema gera appointments automaticamente para as próximas semanas
4. Registra em `auditLog`

### Fluxo 2: Alteração de Agenda Fixa
1. Usuário solicita alteração (pontual ou permanente)
2. Sistema registra em `scheduleChanges`
3. Se pontual: cria/modifica apenas um appointment específico
4. Se permanente: atualiza fixedSchedule e appointments futuros
5. Registra em `auditLog`

### Fluxo 3: Registro de Status de Atendimento
1. Recepção marca status (present/absent/cancelled)
2. Sistema calcula automaticamente:
   - Valor do paciente (customValue ou default, com desconto/pacote)
   - Valor de repasse ao profissional (percentual ou fixo)
3. Atualiza appointment.financial
4. Registra em `auditLog`

### Fluxo 4: Geração de Relatório Mensal
1. Sistema ou Admin dispara geração
2. Busca todos appointments do mês com status != scheduled
3. Agrupa por paciente e por profissional
4. Calcula totais
5. Gera PDF com jsPDF
6. Salva em monthlyReports
7. Registra em `auditLog`

---

## 🔒 Regras de Segurança Firebase (Atualizar)

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "auth != null",
        ".write": "auth != null && (auth.uid == $uid || root.child('users').child(auth.uid).child('role').val() == 'administrator')"
      }
    },
    "specialties": {
      ".read": "auth != null",
      ".write": "auth != null && root.child('users').child(auth.uid).child('role').val() == 'administrator'"
    },
    "professionals": {
      ".read": "auth != null",
      ".write": "auth != null && (root.child('users').child(auth.uid).child('role').val() == 'administrator' || root.child('users').child(auth.uid).child('role').val() == 'reception')"
    },
    "patients": {
      ".read": "auth != null",
      ".write": "auth != null && (root.child('users').child(auth.uid).child('role').val() == 'administrator' || root.child('users').child(auth.uid).child('role').val() == 'reception')"
    },
    "fixedSchedules": {
      ".read": "auth != null",
      ".write": "auth != null && (root.child('users').child(auth.uid).child('role').val() == 'administrator' || root.child('users').child(auth.uid).child('role').val() == 'reception')"
    },
    "appointments": {
      ".read": "auth != null",
      ".write": "auth != null && (root.child('users').child(auth.uid).child('role').val() == 'administrator' || root.child('users').child(auth.uid).child('role').val() == 'reception')"
    },
    "scheduleChanges": {
      ".read": "auth != null && root.child('users').child(auth.uid).child('role').val() == 'administrator'",
      ".write": "auth != null && (root.child('users').child(auth.uid).child('role').val() == 'administrator' || root.child('users').child(auth.uid).child('role').val() == 'reception')"
    },
    "monthlyReports": {
      ".read": "auth != null",
      ".write": "auth != null && (root.child('users').child(auth.uid).child('role').val() == 'administrator' || root.child('users').child(auth.uid).child('role').val() == 'reception')"
    },
    "auditLog": {
      ".read": "auth != null && root.child('users').child(auth.uid).child('role').val() == 'administrator'",
      ".write": "auth != null"
    },
    "systemSettings": {
      ".read": "auth != null",
      ".write": "auth != null && root.child('users').child(auth.uid).child('role').val() == 'administrator'"
    }
  }
}
```

---

## 📱 Offline-First: Dados para Cache Local (IndexedDB)

**Prioridade Alta (sempre em cache):**
- Lista de profissionais ativos
- Lista de especialidades ativas
- Agendas fixas
- Appointments da semana atual e próxima
- Pacientes (lista resumida)

**Prioridade Média (cache sob demanda):**
- Detalhes completos de pacientes
- Histórico de atendimentos

**Apenas Online:**
- Relatórios mensais
- Auditoria
- Dashboard com gráficos

---

**Próximas etapas de implementação:**
1. Atualizar rotas e permissões no código
2. Criar páginas de cadastro (profissionais, especialidades)
3. Aprimorar cadastro de pacientes
4. Implementar agendas fixas
5. Lógica de cálculos financeiros
6. Sistema de relatórios
7. Dashboard com gráficos
8. Auditoria automática
