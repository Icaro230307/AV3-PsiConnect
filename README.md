# PsiConnect

## Projeto de Modelagem Orientada a Objetos

O **PsiConnect** é uma plataforma proposta para auxiliar a gestão de atividades relacionadas ao atendimento psicológico, contemplando profissionais, pacientes, vínculos, disponibilidade, agendamentos, lembretes por e-mail, registros conceituais de atendimento e auditoria.

Este repositório reúne os artefatos desenvolvidos para a disciplina **Análise Orientada a Objetos**, incluindo o dossiê técnico, os diagramas UML em formato editável, o modelo de persistência e os materiais complementares do projeto.

> Todos os nomes, registros profissionais, contatos e demais informações utilizados nos exemplos são fictícios e possuem finalidade exclusivamente acadêmica.

---

## Integrantes

- Davi Rocha Lopes Lima
- Ícaro José Sousa do Carmo
- João Pedro Mascarenhas
- João Vitor Souza
- Pedro Lucas Pinho

---

## Objetivo do projeto

O objetivo do PsiConnect é representar, por meio da Análise Orientada a Objetos, uma solução capaz de centralizar e organizar:

- Usuários;
- Perfis e permissões;
- Psicólogos;
- Pacientes;
- Vínculos profissionais;
- Disponibilidade dos psicólogos;
- Agendamentos;
- Confirmações e cancelamentos;
- Lembretes por e-mail;
- Registros conceituais de atendimento;
- Auditoria das operações.

---

## Escopo

O escopo do PsiConnect contempla:

- Autenticação de usuários;
- Controle de acesso por perfis e permissões;
- Gerenciamento de usuários;
- Gerenciamento de psicólogos;
- Gerenciamento de pacientes;
- Gerenciamento de vínculos entre psicólogos e pacientes;
- Gerenciamento da disponibilidade dos psicólogos;
- Criação, confirmação e cancelamento de agendamentos;
- Consulta da agenda;
- Configuração e envio de lembretes por e-mail;
- Registro do resultado das tentativas de envio;
- Registro conceitual de atendimentos;
- Consulta de auditoria;
- Consulta de informações pelo paciente.

Não fazem parte do escopo:

- Diagnóstico automatizado;
- Prescrição de medicamentos;
- Teleconsulta;
- Pagamentos;
- Prontuário clínico completo;
- Substituição do julgamento profissional.

---

## Estrutura sugerida do repositório

```text
PsiConnect/
├── docs/
│   ├── PsiConnect_AV3.docx
│   └── PsiConnect_AV3.pdf
├── diagramas/
│   ├── editaveis/
│   │   ├── contexto
│   │   ├── casos-de-uso
│   │   ├── classes
│   │   ├── objetos
│   │   ├── sequencias
│   │   ├── comunicacao
│   │   ├── atividades
│   │   ├── estados
│   │   ├── pacotes
│   │   ├── componentes
│   │   ├── implantacao
│   │   └── der
│   └── exportados/
├── modelo-dados/
│   ├── DER
│   └── dicionario-de-dados
├── video/
│   ├── roteiro
│   └── link-do-video
└── README.md
```

---

# Diagramas do Sistema

Os diagramas apresentados a seguir representam a modelagem proposta para o sistema PsiConnect.

---

## 1. Diagrama de Contexto

O diagrama de contexto apresenta os principais atores externos e suas interações com o sistema.

```mermaid
flowchart LR
    Admin["Administrador Geral"]
    Psi["Psicólogo"]
    Pac["Paciente"]
    Email["Serviço de E-mail"]

    Sistema[["Sistema PsiConnect"]]

    Admin -->|"Gerencia usuários, perfis e auditoria"| Sistema
    Psi -->|"Gerencia agenda, vínculos e atendimentos"| Sistema
    Pac -->|"Consulta informações e gerencia agendamentos"| Sistema

    Sistema -->|"Solicita envio de lembretes"| Email
    Email -->|"Retorna resultado do envio"| Sistema
```

---

## 2. Diagrama de Casos de Uso

O diagrama apresenta as funcionalidades disponibilizadas para cada ator.

```mermaid
flowchart LR
    Admin["Administrador Geral"]
    Psi["Psicólogo"]
    Pac["Paciente"]
    Email["Serviço de E-mail"]

    subgraph Sistema["Sistema PsiConnect"]
        UC1(["UC-001: Realizar login"])
        UC2(["UC-002: Gerenciar usuários"])
        UC3(["UC-003: Gerenciar perfis e permissões"])
        UC4(["UC-004: Gerenciar psicólogos"])
        UC5(["UC-005: Gerenciar pacientes"])
        UC6(["UC-006: Gerenciar vínculos"])
        UC7(["UC-007: Gerenciar disponibilidade"])
        UC8(["UC-008: Gerenciar agendamentos"])
        UC9(["UC-009: Confirmar agendamento"])
        UC10(["UC-010: Cancelar agendamento"])
        UC11(["UC-011: Consultar agenda"])
        UC12(["UC-012: Configurar lembretes"])
        UC13(["UC-013: Enviar lembretes"])
        UC14(["UC-014: Consultar situação dos lembretes"])
        UC15(["UC-015: Registrar e consultar atendimento"])
        UC16(["UC-016: Consultar auditoria"])
        UC17(["UC-017: Consultar informações pessoais"])
    end

    Admin --> UC1
    Admin --> UC2
    Admin --> UC3
    Admin --> UC4
    Admin --> UC5
    Admin --> UC6
    Admin --> UC12
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
    Psi --> UC14
    Psi --> UC15

    Pac --> UC1
    Pac --> UC8
    Pac --> UC9
    Pac --> UC10
    Pac --> UC17

    Email --- UC13

    UC13 -.->|"utiliza configuração"| UC12
    UC13 -.->|"registra resultado"| UC14
```

---

## 3. Diagrama de Classes

O diagrama de classes representa as entidades principais, seus atributos, operações e relacionamentos.

```mermaid
classDiagram
    class Usuario {
        <<abstract>>
        -long idUsuario
        -String nome
        -String email
        -String senhaHash
        -String status
        -DateTime criadoEm
        +autenticar(String senha) boolean
        +atualizarDados() void
        +ativar() void
        +bloquear() void
    }

    class Administrador {
        +gerenciarUsuarios() void
        +gerenciarPerfis() void
        +gerenciarPermissoes() void
        +consultarAuditoria() void
    }

    class Psicologo {
        -long idPsicologo
        -String registroProfissional
        -String areaAtuacao
        -boolean ativo
        +atualizarInformacoesProfissionais() void
        +gerenciarDisponibilidade() void
        +consultarAgenda() void
        +registrarAtendimento() void
    }

    class Paciente {
        -long idPaciente
        -String telefone
        -String status
        +atualizarCadastro() void
        +consultarInformacoes() void
        +consultarAgendamentos() void
    }

    class Perfil {
        -long idPerfil
        -String nome
        -String descricao
        -boolean ativo
        +adicionarPermissao() void
        +removerPermissao() void
    }

    class Permissao {
        -long idPermissao
        -String codigo
        -String descricao
    }

    class Vinculo {
        -long idVinculo
        -Date dataInicio
        -Date dataFim
        -String status
        +ativar() void
        +encerrar() void
        +estaAtivo() boolean
    }

    class Disponibilidade {
        -long idDisponibilidade
        -DateTime inicio
        -DateTime fim
        -String status
        +validarIntervalo() boolean
        +verificarSobreposicao() boolean
        +atualizarDisponibilidade() void
    }

    class Agendamento {
        -long idAgendamento
        -DateTime dataHoraInicio
        -DateTime dataHoraFim
        -String status
        -DateTime criadoEm
        +criar() void
        +confirmar() void
        +cancelar() void
        +registrarRealizacao() void
        +registrarFalta() void
        +verificarConflito() boolean
    }

    class ConfiguracaoLembrete {
        -long idConfiguracao
        -int antecedenciaMinutos
        -String canal
        -boolean ativo
        +validar() boolean
        +ativar() void
        +desativar() void
    }

    class Lembrete {
        -long idLembrete
        -DateTime previstoEm
        -DateTime enviadoEm
        -String status
        -String detalheErro
        +registrarTentativa() void
        +marcarComoEnviado() void
        +marcarComoFalha() void
    }

    class Atendimento {
        -long idAtendimento
        -DateTime realizadoEm
        -String registroConceitual
        +registrar() void
        +consultar() void
        +atualizar() void
    }

    class RegistroAuditoria {
        -long idAuditoria
        -String operacao
        -String entidade
        -long idAlvo
        -DateTime ocorridoEm
        -String ipOrigem
        +registrarOperacao() void
        +consultarRegistros() void
    }

    Usuario <|-- Administrador
    Usuario <|-- Psicologo
    Usuario <|-- Paciente

    Usuario "0..*" -- "1..*" Perfil : possui
    Perfil "0..*" -- "0..*" Permissao : contém

    Psicologo "1" --> "0..*" Vinculo : possui
    Paciente "1" --> "0..*" Vinculo : participa

    Psicologo "1" --> "0..*" Disponibilidade : cadastra
    Psicologo "1" --> "0..*" Agendamento : atende
    Paciente "1" --> "0..*" Agendamento : participa

    Agendamento "1" *-- "0..*" Lembrete : possui
    ConfiguracaoLembrete "1" --> "0..*" Lembrete : orienta
    Agendamento "1" --> "0..1" Atendimento : origina

    Usuario "1" --> "0..*" RegistroAuditoria : realiza
```

---

## 4. Diagrama de Objetos

O diagrama apresenta exemplos fictícios de instâncias das classes do sistema.

```mermaid
flowchart TB
    P["psicologo01 : Psicologo<br/>registroProfissional = CRP-FICTICIO<br/>areaAtuacao = Área de Atuação Fictícia"]

    PAC["paciente01 : Paciente<br/>idPaciente = 1<br/>nome = Paciente Exemplo<br/>email = paciente@exemplo.com<br/>status = ATIVO"]

    V["vinculo01 : Vinculo<br/>idVinculo = 1<br/>dataInicio = 01/11/2026<br/>status = ATIVO"]

    D["disponibilidade01 : Disponibilidade<br/>idDisponibilidade = 1<br/>inicio = 10/11/2026 14:00<br/>fim = 10/11/2026 15:00<br/>status = OCUPADO"]

    A["agendamento01 : Agendamento<br/>idAgendamento = 1<br/>inicio = 10/11/2026 14:00<br/>fim = 10/11/2026 15:00<br/>status = CONFIRMADO"]

    C["configuracao01 : ConfiguracaoLembrete<br/>antecedenciaMinutos = 1440<br/>canal = EMAIL<br/>ativo = true"]

    L["lembrete01 : Lembrete<br/>idLembrete = 1<br/>previstoEm = 09/11/2026 14:00<br/>status = PENDENTE"]

    P --> V
    PAC --> V
    P --> D
    P --> A
    PAC --> A
    A --> L
    C --> L
```

---

# Diagramas de Sequência

## 5. Diagrama de Sequência: Realizar Login

```mermaid
sequenceDiagram
    autonumber

    actor Usuario
    participant Interface
    participant Autenticacao
    participant RepositorioUsuario
    participant Auditoria

    Usuario->>Interface: Informar e-mail e senha
    Interface->>Autenticacao: autenticar(email, senha)
    Autenticacao->>RepositorioUsuario: buscarUsuarioPorEmail(email)
    RepositorioUsuario-->>Autenticacao: Retornar usuário

    alt Usuário encontrado e ativo
        Autenticacao->>Autenticacao: Comparar senha com senhaHash

        alt Senha válida
            Autenticacao->>Auditoria: registrarOperacao(LOGIN_SUCESSO)
            Auditoria-->>Autenticacao: Operação registrada
            Autenticacao-->>Interface: Retornar sessão e permissões
            Interface-->>Usuario: Apresentar página inicial
        else Senha inválida
            Autenticacao->>Auditoria: registrarOperacao(LOGIN_FALHA)
            Autenticacao-->>Interface: Informar credenciais inválidas
            Interface-->>Usuario: Exibir mensagem de acesso negado
        end

    else Usuário inexistente ou bloqueado
        Autenticacao-->>Interface: Negar autenticação
        Interface-->>Usuario: Exibir mensagem de acesso negado
    end
```

---

## 6. Diagrama de Sequência: Agendar Consulta

```mermaid
sequenceDiagram
    autonumber

    actor Solicitante
    participant Interface
    participant Agendamento
    participant Vinculo
    participant Disponibilidade
    participant RepositorioAgendamento
    participant Auditoria

    Solicitante->>Interface: Informar psicólogo, paciente e horário
    Interface->>Agendamento: criarAgendamento(dados)

    Agendamento->>Vinculo: verificarVinculoAtivo(psicologo, paciente)
    Vinculo-->>Agendamento: Retornar situação do vínculo

    alt Vínculo ativo
        Agendamento->>Disponibilidade: verificarDisponibilidade(inicio, fim)
        Disponibilidade-->>Agendamento: Retornar disponibilidade

        alt Horário disponível
            Agendamento->>RepositorioAgendamento: verificarConflito(psicologo, inicio, fim)
            RepositorioAgendamento-->>Agendamento: Retornar resultado

            alt Sem conflito
                Agendamento->>RepositorioAgendamento: salvarAgendamento()
                RepositorioAgendamento-->>Agendamento: Agendamento salvo
                Agendamento->>Auditoria: registrarOperacao(CRIAR_AGENDAMENTO)
                Agendamento-->>Interface: Retornar confirmação
                Interface-->>Solicitante: Apresentar agendamento criado
            else Existe conflito
                Agendamento-->>Interface: Informar conflito de horário
                Interface-->>Solicitante: Solicitar outro horário
            end

        else Horário indisponível
            Agendamento-->>Interface: Informar horário indisponível
            Interface-->>Solicitante: Solicitar outro horário
        end

    else Vínculo inativo ou inexistente
        Agendamento-->>Interface: Operação não autorizada
        Interface-->>Solicitante: Informar ausência de vínculo ativo
    end
```

---

## 7. Diagrama de Sequência: Enviar Lembrete

```mermaid
sequenceDiagram
    autonumber

    participant Processador
    participant RepositorioLembrete
    participant RepositorioAgendamento
    participant ServicoEmail
    participant Auditoria

    Processador->>RepositorioLembrete: buscarLembretesPendentes(dataHoraAtual)
    RepositorioLembrete-->>Processador: Retornar lembretes pendentes

    loop Para cada lembrete pendente
        Processador->>RepositorioAgendamento: consultarAgendamento(idAgendamento)
        RepositorioAgendamento-->>Processador: Retornar agendamento

        alt Agendamento ativo e destinatário válido
            Processador->>ServicoEmail: enviarEmail(destinatario, mensagem)

            alt Envio realizado
                ServicoEmail-->>Processador: Retornar sucesso
                Processador->>RepositorioLembrete: marcarComoEnviado(dataHora)
                Processador->>Auditoria: registrarOperacao(LEMBRETE_ENVIADO)
            else Falha no envio
                ServicoEmail-->>Processador: Retornar erro
                Processador->>RepositorioLembrete: marcarComoFalha(detalheErro)
                Processador->>Auditoria: registrarOperacao(FALHA_LEMBRETE)
            end

        else Agendamento cancelado ou destinatário inválido
            Processador->>RepositorioLembrete: marcarComoFalha(motivo)
        end
    end
```

---

## 8. Diagrama de Comunicação

```mermaid
flowchart LR
    P["solicitante : Usuario"]
    A["agendamento : Agendamento"]
    V["vinculo : Vinculo"]
    D["disponibilidade : Disponibilidade"]
    REP["repositorio : RepositorioAgendamento"]
    R["auditoria : RegistroAuditoria"]
    PAC["paciente : Paciente"]

    P -->|"1: criar(psicologo, paciente, inicio, fim)"| A
    A -->|"1.1: verificarVinculoAtivo()"| V
    V -->|"1.2: retornarSituacao()"| A
    A -->|"1.3: verificarDisponibilidade()"| D
    D -->|"1.4: retornarResultado()"| A
    A -->|"1.5: verificarConflito()"| REP
    REP -->|"1.6: retornarResultado()"| A
    A -->|"1.7: salvar()"| REP
    A -->|"1.8: registrarOperacao()"| R
    A -->|"1.9: informarResultado()"| P
    A -->|"1.10: informarAgendamento()"| PAC
```

---

## 9. Diagrama de Atividades

```mermaid
flowchart TD
    Inicio(["Início"])
    A["Usuário acessa a agenda"]
    B["Seleciona psicólogo e paciente"]
    C["Verificar vínculo"]
    D{"Vínculo ativo?"}
    E["Informar data e horário"]
    F["Consultar disponibilidade"]
    G{"Horário disponível?"}
    H["Verificar conflito de agenda"]
    I{"Existe conflito?"}
    J["Criar agendamento"]
    K["Registrar operação na auditoria"]
    L["Apresentar confirmação"]
    M["Informar vínculo inexistente ou inativo"]
    N["Informar horário indisponível"]
    O["Informar conflito de horário"]
    Fim(["Fim"])

    Inicio --> A
    A --> B
    B --> C
    C --> D

    D -->|"Sim"| E
    D -->|"Não"| M

    E --> F
    F --> G

    G -->|"Sim"| H
    G -->|"Não"| N

    H --> I

    I -->|"Não"| J
    I -->|"Sim"| O

    J --> K
    K --> L

    L --> Fim
    M --> Fim
    N --> Fim
    O --> Fim
```

---

## 10. Diagrama de Estados do Agendamento

```mermaid
stateDiagram-v2
    [*] --> Agendado

    Agendado --> Confirmado : confirmar()
    Agendado --> Cancelado : cancelar()

    Confirmado --> Cancelado : cancelar()
    Confirmado --> Realizado : registrarRealizacao()
    Confirmado --> Falta : registrarFalta()

    Cancelado --> [*]
    Realizado --> [*]
    Falta --> [*]
```

### Estados possíveis

- **Agendado:** agendamento criado, aguardando confirmação;
- **Confirmado:** agendamento confirmado;
- **Cancelado:** agendamento cancelado;
- **Realizado:** atendimento realizado;
- **Falta:** paciente não compareceu ao atendimento.

---

## 11. Diagrama de Pacotes

```mermaid
flowchart TB
    subgraph Identidade["Pacote: Identidade e Acesso"]
        Usuario["Usuario"]
        Administrador["Administrador"]
        Perfil["Perfil"]
        Permissao["Permissao"]
    end

    subgraph Profissionais["Pacote: Gestão de Profissionais"]
        Psicologo["Psicologo"]
        Disponibilidade["Disponibilidade"]
    end

    subgraph Pacientes["Pacote: Gestão de Pacientes"]
        Paciente["Paciente"]
        Vinculo["Vinculo"]
    end

    subgraph Agendamentos["Pacote: Agendamentos"]
        Agendamento["Agendamento"]
    end

    subgraph Notificacoes["Pacote: Notificações"]
        Configuracao["ConfiguracaoLembrete"]
        Lembrete["Lembrete"]
    end

    subgraph Atendimentos["Pacote: Atendimentos"]
        Atendimento["Atendimento"]
    end

    subgraph Auditoria["Pacote: Auditoria"]
        Registro["RegistroAuditoria"]
    end

    Identidade --> Profissionais
    Identidade --> Pacientes
    Profissionais --> Pacientes
    Profissionais --> Agendamentos
    Pacientes --> Agendamentos
    Agendamentos --> Notificacoes
    Agendamentos --> Atendimentos
    Agendamentos --> Auditoria
    Atendimentos --> Auditoria
    Identidade --> Auditoria
```

---

## 12. Diagrama de Componentes

```mermaid
flowchart TB
    Admin["Administrador Geral"]
    Psi["Psicólogo"]
    Pac["Paciente"]

    subgraph Sistema["Sistema PsiConnect"]
        UI["Interface Web"]
        API["API PsiConnect"]
        Auth["Autenticação e Controle de Acesso"]
        Usuarios["Gestão de Usuários e Permissões"]
        Cadastros["Gestão de Psicólogos, Pacientes e Vínculos"]
        Agenda["Agenda e Disponibilidade"]
        Agendamentos["Gestão de Agendamentos"]
        Lembretes["Gestão de Lembretes"]
        Atendimentos["Gestão de Atendimentos"]
        Auditoria["Auditoria"]
        Processador["Processador de Lembretes"]
    end

    BD[("Banco de Dados Relacional")]
    Email["Serviço Externo de E-mail"]

    Admin --> UI
    Psi --> UI
    Pac --> UI

    UI --> API

    API --> Auth
    API --> Usuarios
    API --> Cadastros
    API --> Agenda
    API --> Agendamentos
    API --> Lembretes
    API --> Atendimentos
    API --> Auditoria

    Auth --> BD
    Usuarios --> BD
    Cadastros --> BD
    Agenda --> BD
    Agendamentos --> BD
    Lembretes --> BD
    Atendimentos --> BD
    Auditoria --> BD
    Processador --> BD

    Agendamentos --> Cadastros
    Agendamentos --> Agenda
    Agendamentos --> Lembretes
    Agendamentos --> Auditoria

    Usuarios --> Auditoria
    Cadastros --> Auditoria
    Agenda --> Auditoria
    Atendimentos --> Auditoria
    Lembretes --> Auditoria

    Processador --> Lembretes
    Processador --> Email
```

---

## 13. Diagrama de Implantação

O diagrama de implantação apresenta os nós físicos e serviços envolvidos na execução da solução.

```mermaid
flowchart LR
    subgraph Cliente["Dispositivo do usuário"]
        Navegador["Navegador Web"]
    end

    subgraph ServidorAplicacao["Servidor de Aplicação"]
        Web["Interface Web"]
        API["API PsiConnect"]
        Processador["Processador de Lembretes"]
    end

    subgraph ServidorBanco["Servidor de Banco de Dados"]
        Banco[("Banco de Dados Relacional")]
    end

    Email["Serviço Externo de E-mail"]

    Navegador -->|"HTTPS"| Web
    Web -->|"HTTPS/JSON"| API
    API -->|"Conexão segura"| Banco
    Processador -->|"Conexão segura"| Banco
    Processador -->|"HTTPS/API"| Email
```

---

# Modelo de Persistência

## 14. Diagrama Entidade-Relacionamento

```mermaid
erDiagram
    USUARIO {
        bigint id_usuario PK
        varchar nome
        varchar email UK
        varchar senha_hash
        varchar status
        timestamp criado_em
    }

    PERFIL {
        bigint id_perfil PK
        varchar nome UK
        varchar descricao
        boolean ativo
    }

    PERMISSAO {
        bigint id_permissao PK
        varchar codigo UK
        varchar descricao
    }

    USUARIO_PERFIL {
        bigint id_usuario PK, FK
        bigint id_perfil PK, FK
    }

    PERFIL_PERMISSAO {
        bigint id_perfil PK, FK
        bigint id_permissao PK, FK
    }

    PSICOLOGO {
        bigint id_psicologo PK
        bigint id_usuario FK, UK
        varchar registro_profissional UK
        varchar area_atuacao
        boolean ativo
    }

    PACIENTE {
        bigint id_paciente PK
        bigint id_usuario FK, UK
        varchar nome
        varchar email
        varchar telefone
        varchar status
    }

    VINCULO {
        bigint id_vinculo PK
        bigint id_psicologo FK
        bigint id_paciente FK
        date data_inicio
        date data_fim
        varchar status
    }

    DISPONIBILIDADE {
        bigint id_disponibilidade PK
        bigint id_psicologo FK
        timestamp inicio
        timestamp fim
        varchar status
    }

    AGENDAMENTO {
        bigint id_agendamento PK
        bigint id_psicologo FK
        bigint id_paciente FK
        bigint criado_por FK
        timestamp inicio
        timestamp fim
        varchar status
        timestamp criado_em
    }

    CONFIGURACAO_LEMBRETE {
        bigint id_configuracao PK
        int antecedencia_minutos
        varchar canal
        boolean ativo
    }

    LEMBRETE {
        bigint id_lembrete PK
        bigint id_agendamento FK
        bigint id_configuracao FK
        timestamp previsto_em
        timestamp enviado_em
        varchar status
        varchar detalhe_erro
    }

    ATENDIMENTO {
        bigint id_atendimento PK
        bigint id_agendamento FK, UK
        bigint criado_por FK
        timestamp realizado_em
        text registro_conceitual
    }

    REGISTRO_AUDITORIA {
        bigint id_auditoria PK
        bigint id_usuario FK
        varchar operacao
        varchar entidade
        bigint id_alvo
        timestamp ocorrido_em
        varchar ip_origem
    }

    USUARIO ||--o{ USUARIO_PERFIL : possui
    PERFIL ||--o{ USUARIO_PERFIL : atribuido

    PERFIL ||--o{ PERFIL_PERMISSAO : possui
    PERMISSAO ||--o{ PERFIL_PERMISSAO : atribuida

    USUARIO ||--o| PSICOLOGO : representa
    USUARIO ||--o| PACIENTE : representa

    PSICOLOGO ||--o{ VINCULO : possui
    PACIENTE ||--o{ VINCULO : participa

    PSICOLOGO ||--o{ DISPONIBILIDADE : cadastra
    PSICOLOGO ||--o{ AGENDAMENTO : atende
    PACIENTE ||--o{ AGENDAMENTO : participa
    USUARIO ||--o{ AGENDAMENTO : cria

    AGENDAMENTO ||--o{ LEMBRETE : possui
    CONFIGURACAO_LEMBRETE ||--o{ LEMBRETE : configura

    AGENDAMENTO ||--o| ATENDIMENTO : origina
    USUARIO ||--o{ ATENDIMENTO : registra

    USUARIO ||--o{ REGISTRO_AUDITORIA : realiza
```

---

## 15. Regras de Integridade do Banco de Dados

O modelo de persistência deverá respeitar as seguintes regras:

1. O e-mail do usuário deve ser único.
2. O registro profissional do psicólogo deve ser único.
3. Todo psicólogo deve estar relacionado a um usuário.
4. O paciente pode estar relacionado a um usuário para acesso ao sistema.
5. Todo vínculo deve relacionar um psicólogo e um paciente.
6. A data final do vínculo não pode ser anterior à data inicial.
7. O horário final da disponibilidade deve ser posterior ao horário inicial.
8. O horário final do agendamento deve ser posterior ao horário inicial.
9. Um psicólogo não pode possuir agendamentos confirmados sobrepostos.
10. Todo agendamento deve estar relacionado a um psicólogo e um paciente.
11. Todo lembrete deve estar relacionado a um agendamento.
12. Um atendimento só pode ser registrado para agendamento realizado.
13. Cada agendamento pode possuir no máximo um atendimento.
14. Operações críticas devem gerar um registro de auditoria.
15. Exclusões de usuários, psicólogos e pacientes devem ser preferencialmente lógicas.

---

# Regras de Negócio Principais

- **RN-001:** somente usuários ativos e autorizados podem acessar o sistema.
- **RN-002:** o e-mail do usuário e o registro profissional devem ser únicos.
- **RN-003:** o psicólogo pode acessar apenas pacientes com vínculo ativo.
- **RN-004:** todo agendamento deve ocorrer dentro da disponibilidade do psicólogo.
- **RN-005:** não são permitidos agendamentos confirmados sobrepostos.
- **RN-006:** o cancelamento deve alterar a situação e liberar o intervalo.
- **RN-007:** somente agendamentos ativos podem ser confirmados ou cancelados.
- **RN-008:** lembretes devem ser enviados somente para agendamentos ativos.
- **RN-009:** cada tentativa de envio deve gerar um registro de resultado.
- **RN-010:** o atendimento só pode ser registrado para um agendamento realizado.
- **RN-011:** operações críticas devem gerar registros de auditoria.
- **RN-012:** todos os dados utilizados no projeto devem ser fictícios.
- **RN-013:** o paciente pode consultar apenas as próprias informações.
- **RN-014:** senhas devem ser armazenadas por meio de hash seguro.
- **RN-015:** exclusões lógicas devem preservar o histórico do sistema.

---

# Arquitetura Proposta

A arquitetura do PsiConnect está dividida nas seguintes camadas:

## Camada de apresentação

Responsável pela interação com os usuários por meio da interface web.

## Camada de aplicação

Responsável pela coordenação dos casos de uso, autenticação, validações e controle das operações.

## Camada de domínio

Responsável pelas entidades, regras de negócio, estados, relacionamentos e comportamentos do sistema.

## Camada de infraestrutura

Responsável pela persistência, conexão com o banco de dados, auditoria e integração com o serviço externo de e-mail.

---

# Tecnologias de Modelagem

Os artefatos do projeto utilizam:

- UML 2.x;
- Mermaid;
- Markdown;
- Modelo relacional;
- Git;
- GitHub.

---

# Visualização dos Diagramas

Os diagramas Mermaid podem ser visualizados diretamente no GitHub.

Os arquivos editáveis também devem ser mantidos no diretório:

```text
diagramas/editaveis/
```

As versões exportadas em PNG ou PDF devem ser mantidas no diretório:

```text
diagramas/exportados/
```

---

# Documento do Projeto

O documento completo do projeto está disponível no diretório:

```text
docs/
```

O diretório deve conter:

- Documento editável em formato DOCX;
- Documento final em formato PDF.

---

# Vídeo-pitch

O roteiro e o link do vídeo-pitch deverão ser disponibilizados no diretório:

```text
video/
```

O vídeo deverá apresentar resumidamente:

1. O problema identificado;
2. A proposta do PsiConnect;
3. Os principais atores;
4. As funcionalidades;
5. Os diagramas mais importantes;
6. O modelo de persistência;
7. A arquitetura;
8. Os benefícios esperados.

---

# Considerações Finais

O PsiConnect apresenta uma proposta de modelagem integrada para organização de usuários, profissionais, pacientes, vínculos, disponibilidade, agendamentos, lembretes, atendimentos e auditoria.

Os artefatos deste repositório foram organizados para manter a rastreabilidade e a coerência entre requisitos, regras de negócio, casos de uso, classes, interações, arquitetura e persistência.

O projeto possui finalidade exclusivamente acadêmica e não utiliza dados reais de pacientes ou profissionais.
