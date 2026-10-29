# AV3-PsiConnect

Projeto acadêmico desenvolvido para a avaliação AV3 da disciplina de Análise Orientada a Objetos.

O **PsiConnect** é uma proposta de plataforma voltada à organização de informações de psicólogos, pacientes, disponibilidades, agendamentos e lembretes de consultas.

## 1. Diagrama de Classes

O diagrama de classes representa a estrutura do PsiConnect, apresentando suas principais classes, atributos, operações e relacionamentos.

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

## 2. Diagrama de Estados — Agendamento

O diagrama de estados representa o ciclo de vida de um agendamento, desde a solicitação até sua confirmação, cancelamento, realização ou registro de não comparecimento.

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
## 3. Diagrama de Sequência — Agendar e Confirmar Consulta

O diagrama de sequência representa a ordem temporal das mensagens trocadas entre os objetos no cenário de agendamento e confirmação de uma consulta, incluindo a verificação de disponibilidade, a confirmação ou recusa pelo psicólogo e o envio do lembrete.

```mermaid
sequenceDiagram
    autonumber
    actor Pac as Paciente
    actor Psi as Psicólogo
    participant D as :Disponibilidade
    participant A as :Agendamento
    participant L as :Lembrete
    participant R as :RegistroAuditoria

    Pac->>D: consultarHorarios(idPsicologo)
    D-->>Pac: horários disponíveis

    Pac->>A: agendarConsulta(idPsicologo, dataHora)
    A->>D: verificarDisponibilidade(dataHora)

    alt horário disponível
        D-->>A: disponível
        A->>A: definirStatus("Solicitado")
        A->>R: registrarOperacao("Agendamento solicitado")
        A-->>Pac: solicitação registrada
    else horário indisponível
        D-->>A: indisponível
        A-->>Pac: horário indisponível, escolha outro
    end

    Psi->>A: consultarAgendamentos()
    A-->>Psi: lista de agendamentos pendentes

    alt psicólogo confirma
        Psi->>A: confirmarConsulta(idAgendamento)
        A->>A: definirStatus("Confirmado")
        A->>R: registrarOperacao("Agendamento confirmado")
        A->>L: enviarLembrete(antecedencia, canal)
        L-->>Pac: lembrete da consulta
        A-->>Psi: confirmação concluída
    else psicólogo recusa
        Psi->>A: cancelarConsulta(idAgendamento)
        A->>A: definirStatus("Cancelado")
        A->>R: registrarOperacao("Agendamento cancelado")
        A-->>Pac: notificação de cancelamento
    end
```

## 4. Diagrama de Comunicação — Agendar e Confirmar Consulta

O diagrama de comunicação representa o mesmo cenário do diagrama de sequência, enfatizando os vínculos entre os objetos e a numeração sequencial das mensagens. Como o Mermaid não possui sintaxe nativa para diagramas de comunicação, ele foi representado com `flowchart`, usando objetos como nós e mensagens numeradas nas setas.

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
