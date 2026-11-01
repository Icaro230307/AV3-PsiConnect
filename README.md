
# AV3-PsiConnect

Projeto acadêmico desenvolvido para a avaliação AV3 da disciplina de Análise Orientada a Objetos.

O **PsiConnect** é uma proposta de plataforma voltada à organização de informações de psicólogos, pacientes, disponibilidades, agendamentos, atendimentos e lembretes de consultas.

Este repositório contém os diagramas utilizados na modelagem conceitual do projeto.

---

## 1. Diagrama de Classes

Representa as classes, seus atributos, operações e relacionamentos.

```mermaid
classDiagram
    class Usuario {
        -int idUsuario
        -String nome
        -String email
        -String senhaHash
        -String status
    }

    class Administrador {
        -int idAdministrador
        +gerenciarUsuarios()
        +consultarAuditoria()
    }

    class Psicologo {
        -int idPsicologo
        -String registroProfissional
        -String areaAtuacao
        +gerenciarDisponibilidade()
        +consultarAgendamentos()
    }

    class Paciente {
        -int idPaciente
        -String nome
        -String contato
        -String status
    }

    class Disponibilidade {
        -int idDisponibilidade
        -Date data
        -Time horaInicio
        -Time horaFim
    }

    class Agendamento {
        -int idAgendamento
        -DateTime dataHora
        -String status
        +agendarConsulta()
        +cancelarConsulta()
        +confirmarConsulta()
    }

    class Lembrete {
        -int idLembrete
        -String antecedencia
        -String canal
        -String statusEnvio
        +enviarLembrete()
    }

    class Atendimento {
        -int idAtendimento
        -Date data
        -String registroConceitual
        +registrarAtendimento()
    }

    class RegistroAuditoria {
        -int idAuditoria
        -String operacao
        -DateTime dataHora
        +registrarOperacao()
    }

    Usuario <|-- Administrador
    Usuario <|-- Psicologo

    Psicologo "1" --> "0..*" Disponibilidade : possui
    Psicologo "1" --> "0..*" Agendamento : atende
    Paciente "1" --> "0..*" Agendamento : solicita
    Agendamento "1" --> "0..*" Lembrete : possui
    Agendamento "1" --> "0..1" Atendimento : gera
    Usuario "1" --> "0..*" RegistroAuditoria : realiza
```

---

## 2. Diagrama de Estados — Agendamento

Representa o ciclo de vida de um agendamento, desde a solicitação até sua conclusão, cancelamento ou não comparecimento.

```mermaid
stateDiagram-v2
    [*] --> Solicitado

    Solicitado --> Confirmado : Consulta confirmada
    Solicitado --> Cancelado : Solicitação cancelada

    Confirmado --> Cancelado : Consulta cancelada
    Confirmado --> Realizado : Atendimento concluído
    Confirmado --> NaoCompareceu : Paciente não compareceu

    Cancelado --> [*]
    Realizado --> [*]
    NaoCompareceu --> [*]

    state "Não compareceu" as NaoCompareceu
```

---

## 3. Diagrama de Sequência — Agendar e Confirmar Consulta

Representa a ordem temporal das mensagens trocadas durante o agendamento e a confirmação de uma consulta.

```mermaid
sequenceDiagram
    autonumber
    actor Pac as Paciente
    actor Psi as Psicólogo
    participant D as Disponibilidade
    participant A as Agendamento
    participant L as Lembrete
    participant R as RegistroAuditoria

    Pac->>D: consultarHorarios(idPsicologo)
    D-->>Pac: horariosDisponiveis

    Pac->>A: agendarConsulta(idPsicologo, dataHora)
    A->>D: verificarDisponibilidade(dataHora)

    alt Horário disponível
        D-->>A: disponível
        A->>R: registrarOperacao(Agendamento solicitado)
        A-->>Pac: solicitação registrada
    else Horário indisponível
        D-->>A: indisponível
        A-->>Pac: escolher outro horário
    end

    Psi->>A: consultarAgendamentos()

    alt Psicólogo confirma
        Psi->>A: confirmarConsulta(idAgendamento)
        A->>R: registrarOperacao(Agendamento confirmado)
        A->>L: enviarLembrete(antecedencia, canal)
        L-->>Pac: lembrete da consulta
        A-->>Psi: confirmação concluída
    else Psicólogo recusa
        Psi->>A: cancelarConsulta(idAgendamento)
        A->>R: registrarOperacao(Agendamento cancelado)
        A-->>Pac: notificação de cancelamento
    end
```

---

## 4. Diagrama de Comunicação — Agendar e Confirmar Consulta

Representa os objetos envolvidos e as mensagens trocadas no processo de agendamento.

```mermaid
flowchart LR
    Pac(["Paciente"])
    Psi(["Psicólogo"])
    D["d : Disponibilidade"]
    A["a : Agendamento"]
    L["l : Lembrete"]
    R["r : RegistroAuditoria"]

    Pac -->|"1: consultarHorarios()"| D
    Pac -->|"2: agendarConsulta()"| A
    A -->|"3: verificarDisponibilidade()"| D
    A -->|"4: registrarOperacao()"| R
    Psi -->|"5: consultarAgendamentos()"| A
    Psi -->|"6: confirmarConsulta()"| A
    A -->|"7: registrarOperacao()"| R
    A -->|"8: enviarLembrete()"| L
    L -->|"9: lembrete da consulta"| Pac
```

---

## 5. Diagrama de Componentes — PsiConnect

Apresenta os principais módulos funcionais do sistema e suas dependências.

```mermaid
flowchart TB
    subgraph Atores["Atores do Sistema"]
        ADM["Administrador"]
        PSI["Psicólogo"]
        PAC["Paciente"]
    end

    subgraph Sistema["Sistema PsiConnect"]
        UI["Interface do Usuário"]
        AUTH["Identidade e Acesso"]
        GEST["Gestão de Usuários"]
        AGENDA["Agenda e Disponibilidade"]
        AGEND["Gestão de Agendamentos"]
        LEM["Gestão de Lembretes"]
        ATEND["Gestão de Atendimento"]
        AUD["Auditoria"]
    end

    DB[("Banco de Dados Relacional")]
    NOTIF["Serviço de Notificações"]

    ADM --> UI
    PSI --> UI
    PAC --> UI

    UI --> AUTH
    UI --> GEST
    UI --> AGENDA
    UI --> AGEND
    UI --> ATEND

    AUTH --> DB
    GEST --> DB
    AGENDA --> DB
    AGEND --> DB
    ATEND --> DB
    AUD --> DB
    LEM --> DB

    AGEND --> LEM
    LEM --> NOTIF

    AUTH -.-> AUD
    GEST -.-> AUD
    AGENDA -.-> AUD
    AGEND -.-> AUD
    ATEND -.-> AUD
```

---

## 6. Diagrama de Implantação — PsiConnect

Representa uma proposta de distribuição da plataforma entre o dispositivo do usuário, o servidor de aplicação e o banco de dados.

```mermaid
flowchart TB
    subgraph Cliente["Dispositivo do Usuário"]
        BROWSER["Navegador Web"]
        UI["Interface do PsiConnect"]
        BROWSER --> UI
    end

    subgraph ServidorApp["Servidor de Aplicação"]
        APP["Aplicação PsiConnect"]
        AUTH["Serviço de Identidade e Acesso"]
        MOD["Módulos de Agenda, Agendamento, Atendimento e Administração"]
        LEM["Módulo de Lembretes"]
        AUD["Módulo de Auditoria"]

        APP --> AUTH
        APP --> MOD
        APP --> LEM
        APP --> AUD
    end

    subgraph ServidorBD["Servidor de Banco de Dados"]
        DB[("Banco de Dados Relacional")]
    end

    NOTIF["Serviço de Notificações"]

    UI <-->|"HTTPS"| APP
    AUTH <-->|"Acesso aos dados"| DB
    MOD <-->|"Consulta e persistência"| DB
    LEM <-->|"Dados dos lembretes"| DB
    AUD <-->|"Registros de auditoria"| DB
    LEM -->|"Envio de lembretes"| NOTIF
```

---

## 7. Diagrama de Casos de Uso — PsiConnect

Representa os atores que interagem com a plataforma e as principais funcionalidades disponíveis para cada perfil.

```mermaid
flowchart LR
    Paciente([Paciente])
    Psicologo([Psicólogo])
    Administrador([Administrador])

    subgraph PsiConnect["Sistema PsiConnect"]
        UC1([Cadastrar-se])
        UC2([Realizar login])
        UC3([Gerenciar perfil])
        UC4([Buscar psicólogo])
        UC5([Agendar consulta])
        UC6([Cancelar consulta])
        UC7([Confirmar consulta])
        UC8([Realizar atendimento])
        UC9([Gerenciar usuários])
        UC10([Receber lembrete])
        UC11([Gerenciar disponibilidade])
        UC12([Consultar agendamentos])
    end

    Paciente --> UC1
    Paciente --> UC2
    Paciente --> UC3
    Paciente --> UC4
    Paciente --> UC5
    Paciente --> UC6
    Paciente --> UC10

    Psicologo --> UC2
    Psicologo --> UC3
    Psicologo --> UC7
    Psicologo --> UC8
    Psicologo --> UC11
    Psicologo --> UC12

    Administrador --> UC2
    Administrador --> UC9
```

---

## 8. Diagrama de Objetos — Exemplo de Agendamento

Representa instâncias concretas das classes em um cenário específico de consulta.

```mermaid
classDiagram
    direction LR

    class pacienteJoao {
        <<Paciente>>
        idPaciente = 1
        nome = "João"
        contato = "joao@email.com"
        status = "Ativo"
    }

    class psicologoAna {
        <<Psicologo>>
        idPsicologo = 2
        registroProfissional = "CRP-exemplo"
        areaAtuacao = "Psicologia Clínica"
    }

    class agendamento01 {
        <<Agendamento>>
        idAgendamento = 1
        dataHora = "2026-11-10 14:00"
        status = "Confirmado"
    }

    class lembrete01 {
        <<Lembrete>>
        idLembrete = 1
        antecedencia = "24 horas"
        canal = "Email"
        statusEnvio = "Pendente"
    }

    pacienteJoao --> agendamento01 : solicita
    psicologoAna --> agendamento01 : atende
    agendamento01 --> lembrete01 : possui
```

---
