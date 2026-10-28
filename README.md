
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

