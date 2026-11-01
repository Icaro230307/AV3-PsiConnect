# PsiConnect

## Projeto de Modelagem Orientada a Objetos

O PsiConnect é uma plataforma proposta para auxiliar a gestão de atividades relacionadas ao atendimento psicológico, contemplando profissionais, pacientes, disponibilidade, agendamentos, lembretes, registros de atendimento e auditoria.

# Diagramas do Sistema

Os diagramas abaixo representam a modelagem proposta para o sistema PsiConnect.

---

## 1. Diagrama de Contexto

```mermaid
flowchart LR
    Admin["Administrador Geral"]
    Psi["Psicólogo"]
    Pac["Cliente/Paciente"]
    Notif["Serviço de Notificações"]

    Sistema[["Sistema PsiConnect"]]

    Admin -->|"Gerencia o sistema"| Sistema
    Psi -->|"Gerencia agenda e atendimentos"| Sistema
    Pac -->|"Relaciona-se com os serviços"| Sistema
    Sistema -->|"Solicita envio de lembretes"| Notif
    Notif -->|"Retorna situação das notificações"| Sistema
```

---

## 2. Diagrama de Casos de Uso

```mermaid
flowchart LR
    Admin["Administrador Geral"]
    Psi["Psicólogo"]
    Pac["Cliente/Paciente"]
    Notif["Serviço de Notificações"]

    subgraph Sistema["Sistema PsiConnect"]
        UC1(["Realizar login"])
        UC2(["Gerenciar usuários"])
        UC3(["Gerenciar perfis e permissões"])
        UC4(["Gerenciar informações profissionais"])
        UC5(["Gerenciar pacientes"])
        UC6(["Gerenciar vínculos profissionais"])
        UC7(["Gerenciar disponibilidade"])
        UC8(["Gerenciar agendamentos"])
        UC9(["Confirmar agendamento"])
        UC10(["Cancelar agendamento"])
        UC11(["Consultar agenda"])
        UC12(["Configurar lembretes"])
        UC13(["Enviar lembretes"])
        UC14(["Consultar situação dos lembretes"])
        UC15(["Consultar registros de atendimento"])
        UC16(["Consultar auditoria"])
        UC17(["Consultar informações pessoais"])
    end

    Admin --> UC1
    Admin --> UC2
    Admin --> UC3
    Admin --> UC5
    Admin --> UC14
    Admin --> UC16

    Psi --> UC1
    Psi --> UC4
    Psi --> UC5
    Psi --> UC6
    Psi --> UC7
    Psi --> UC8
    Psi --> UC9
    Psi --> UC10
    Psi --> UC11
    Psi --> UC12
    Psi --> UC14
    Psi --> UC15

    Pac --> UC1
    Pac --> UC17

    Notif --> UC13
```

---

## 3. Diagrama de Classes

```mermaid
classDiagram
    class Usuario {
        <<abstract>>
        -int idUsuario
        -String nome
        -String email
        -String senhaHash
        -String status
        +autenticar() boolean
        +atualizarPerfil() void
    }

    class Administrador {
        +gerenciarUsuarios() void
        +gerenciarPermissoes() void
        +consultarAuditoria() void
    }

    class Psicologo {
        -String registroProfissional
        -String areaAtuacao
        +atualizarInformacoesProfissionais() void
        +gerenciarDisponibilidade() void
        +consultarAgenda() void
        +confirmarAgendamento() void
    }

    class Paciente {
        -int idPaciente
        -String nome
        -String contato
        -String status
        +atualizarCadastro() void
        +consultarInformacoes() void
    }

    class Disponibilidade {
        -int idDisponibilidade
        -Date data
        -Time horaInicio
        -Time horaFim
        -String status
        +verificarDisponibilidade() boolean
        +atualizarDisponibilidade() void
    }

    class Agendamento {
        -int idAgendamento
        -DateTime dataHora
        -String status
        +criar() void
        +confirmar() void
        +cancelar() void
        +realizar() void
        +verificarConflito() boolean
    }

    class Lembrete {
        -int idLembrete
        -String antecedencia
        -String canal
        -String statusEnvio
        +configurar() void
        +enviar() void
        +registrarTentativa() void
        +consultarSituacao() String
    }

    class Atendimento {
        -int idAtendimento
        -Date data
        -String registroConceitual
        +registrar() void
        +consultar() void
    }

    class RegistroAuditoria {
        -int idAuditoria
        -String operacao
        -DateTime dataHora
        +registrarOperacao() void
        +consultarRegistros() void
    }

    Usuario <|-- Administrador
    Usuario <|-- Psicologo

    Psicologo "1" --> "0..*" Disponibilidade : cadastra
    Psicologo "1" --> "0..*" Agendamento : atende
    Paciente "1" --> "0..*" Agendamento : participa

    Agendamento "1" *-- "0..*" Lembrete : possui
    Agendamento "1" --> "0..1" Atendimento : origina

    Usuario "1" --> "0..*" RegistroAuditoria : realiza
```

---

## 4. Diagrama de Objetos

```mermaid
flowchart TB
    P["psicologo01 : Psicologo<br/>registroProfissional = CRP-EXEMPLO<br/>areaAtuacao = Psicologia Clínica"]

    PAC["paciente01 : Paciente<br/>idPaciente = 1<br/>nome = Paciente Exemplo<br/>contato = exemplo@email.com<br/>status = Ativo"]

    D["disponibilidade01 : Disponibilidade<br/>idDisponibilidade = 1<br/>data = 10/11/2026<br/>horaInicio = 14:00<br/>horaFim = 15:00<br/>status = Ocupado"]

    A["agendamento01 : Agendamento<br/>idAgendamento = 1<br/>dataHora = 10/11/2026 14:00<br/>status = Confirmado"]

    L["lembrete01 : Lembrete<br/>idLembrete = 1<br/>antecedencia = 24 horas<br/>canal = Email<br/>statusEnvio = Pendente"]

    P --> D
    P --> A
    PAC --> A
    A --> L
```

---

## 5. Diagrama de Sequência

```mermaid
sequenceDiagram
    autonumber

    actor Psicologo
    participant Interface
    participant Agendamento
    participant Disponibilidade
    participant Auditoria

    Psicologo->>Interface: Informar paciente e horário
    Interface->>Agendamento: Criar agendamento
    Agendamento->>Disponibilidade: Verificar disponibilidade
    Disponibilidade-->>Agendamento: Retornar resultado

    alt Horário disponível e sem conflito
        Agendamento->>Agendamento: Criar agendamento
        Agendamento->>Auditoria: Registrar operação
        Agendamento-->>Interface: Agendamento criado
        Interface-->>Psicologo: Apresentar confirmação
    else Horário indisponível ou conflito
        Agendamento-->>Interface: Operação não permitida
        Interface-->>Psicologo: Informar indisponibilidade
    end
```

---

## 6. Diagrama de Comunicação

```mermaid
flowchart LR
    P["psicologo : Psicologo"]
    A["agendamento : Agendamento"]
    D["disponibilidade : Disponibilidade"]
    PAC["paciente : Paciente"]
    R["auditoria : RegistroAuditoria"]

    P -->|"1: criar(paciente, dataHora)"| A
    A -->|"1.1: verificarDisponibilidade()"| D
    D -->|"1.2: retornarResultado()"| A
    A -->|"1.3: verificarConflito()"| A
    A -->|"1.4: registrarOperacao()"| R
    A -->|"1.5: informarResultado()"| P
    A -->|"1.6: informarAgendamento()"| PAC
```

---

## 7. Diagrama de Atividades

```mermaid
flowchart TD
    Inicio(["Início"])
    A["Psicólogo acessa a agenda"]
    B["Seleciona paciente"]
    C["Informa data e horário"]
    D["Consultar disponibilidade"]
    E{"Horário disponível?"}
    F["Verificar conflito de agenda"]
    G{"Existe conflito?"}
    H["Criar agendamento"]
    I["Registrar operação na auditoria"]
    J["Apresentar confirmação"]
    K["Informar conflito de horário"]
    L["Informar horário indisponível"]
    Fim(["Fim"])

    Inicio --> A
    A --> B
    B --> C
    C --> D
    D --> E

    E -->|"Sim"| F
    E -->|"Não"| L

    F --> G
    G -->|"Não"| H
    G -->|"Sim"| K

    H --> I
    I --> J

    J --> Fim
    K --> Fim
    L --> Fim
```

---

## 8. Diagrama de Estados

```mermaid
stateDiagram-v2
    [*] --> Agendado

    Agendado --> Confirmado : Confirmar
    Agendado --> Cancelado : Cancelar

    Confirmado --> Cancelado : Cancelar
    Confirmado --> Realizado : Realizar atendimento

    Cancelado --> [*]
    Realizado --> [*]
```

---

## 9. Diagrama de Pacotes

```mermaid
flowchart TB
    subgraph Identidade["Pacote: Identidade e Acesso"]
        Usuario["Usuario"]
        Administrador["Administrador"]
    end

    subgraph Profissionais["Pacote: Gestão de Profissionais"]
        Psicologo["Psicologo"]
        Disponibilidade["Disponibilidade"]
    end

    subgraph Pacientes["Pacote: Gestão de Pacientes"]
        Paciente["Paciente"]
    end

    subgraph Agendamentos["Pacote: Agendamentos"]
        Agendamento["Agendamento"]
        Lembrete["Lembrete"]
    end

    subgraph Atendimentos["Pacote: Atendimentos"]
        Atendimento["Atendimento"]
    end

    subgraph Auditoria["Pacote: Auditoria"]
        Registro["RegistroAuditoria"]
    end

    Identidade --> Profissionais
    Profissionais --> Agendamentos
    Pacientes --> Agendamentos
    Agendamentos --> Atendimentos
    Agendamentos --> Auditoria
    Identidade --> Auditoria
```

---

## 10. Diagrama de Componentes

```mermaid
flowchart TB
    Admin["Administrador Geral"]
    Psi["Psicólogo"]
    Pac["Cliente/Paciente"]

    subgraph Sistema["Sistema PsiConnect"]
        UI["Interface do Usuário"]
        Auth["Autenticação e Controle de Acesso"]
        Usuarios["Gestão de Usuários e Permissões"]
        Cadastros["Gestão de Psicólogos e Pacientes"]
        Agenda["Agenda e Disponibilidade"]
        Agendamentos["Gestão de Agendamentos"]
        Lembretes["Gestão de Lembretes"]
        Atendimentos["Gestão de Atendimentos"]
        Auditoria["Auditoria"]
    end

    BD[("Banco de Dados Relacional")]
    Notificacao["Serviço de Notificações"]

    Admin --> UI
    Psi --> UI
    Pac --> UI

    UI --> Auth
    UI --> Usuarios
    UI --> Cadastros
    UI --> Agenda
    UI --> Agendamentos
    UI --> Atendimentos

    Auth --> BD
    Usuarios --> BD
    Cadastros --> BD
    Agenda --> BD
    Agendamentos --> BD
    Lembretes --> BD
    Atendimentos --> BD
    Auditoria --> BD

    Agendamentos --> Agenda
    Agendamentos --> Lembretes
    Agendamentos --> Auditoria
    Atendimentos --> Auditoria
    Lembretes --> Notificacao
```

---

**Observação:** os diagramas foram representados com Mermaid para permitir a visualização diretamente no GitHub e a edição do código no README. Algumas representações, como comunicação, pacotes, componentes e objetos, são aproximações visuais, pois Mermaid não oferece uma notação UML nativa específica para todas elas.
