
' =========================================================
' PROJETO: MENTEVIVA CONNECT / PSICONNECT
' COLECAO DOS 10 DIAGRAMAS UML
' =========================================================


' =========================================================
' 1. DIAGRAMA DE CONTEXTO
' =========================================================
@startuml
title 1 - Diagrama de Contexto
left to right direction

actor "Administrador Geral" as Admin
actor "Psicólogo" as Psi
actor "Cliente/Paciente" as Pac
actor "Serviço de Notificações" as Notif

rectangle "PsiConnect" {
  usecase "Gerenciar usuários,\nperfis e permissões" as UC1
  usecase "Gerenciar informações\nprofissionais" as UC2
  usecase "Gerenciar pacientes\ne vínculos" as UC3
  usecase "Gerenciar disponibilidade\ne agenda" as UC4
  usecase "Gerenciar agendamentos" as UC5
  usecase "Gerenciar lembretes" as UC6
  usecase "Consultar registros\nde atendimento" as UC7
  usecase "Consultar auditoria" as UC8
}

Admin --> UC1
Admin --> UC3
Admin --> UC8

Psi --> UC2
Psi --> UC3
Psi --> UC4
Psi --> UC5
Psi --> UC7

Pac --> UC5 : conforme escopo final
Pac --> UC6 : conforme escopo final

UC6 --> Notif : solicita envio
@enduml


' =========================================================
' 2. DIAGRAMA DE CASOS DE USO
' =========================================================
@startuml
title 2 - Diagrama de Casos de Uso
left to right direction

actor "Administrador Geral" as Admin
actor "Psicólogo" as Psi
actor "Cliente/Paciente" as Pac
actor "Serviço de Notificações" as Notif

rectangle "Sistema PsiConnect" {
  usecase "UC-001\nRealizar login" as UC001
  usecase "UC-002\nGerenciar usuários" as UC002
  usecase "UC-003\nGerenciar perfis e permissões" as UC003
  usecase "UC-004\nGerenciar informações profissionais" as UC004
  usecase "UC-005\nGerenciar pacientes" as UC005
  usecase "UC-006\nGerenciar vínculos profissionais" as UC006
  usecase "UC-007\nGerenciar disponibilidade" as UC007
  usecase "UC-008\nGerenciar agendamentos" as UC008
  usecase "UC-009\nConfirmar agendamento" as UC009
  usecase "UC-010\nCancelar agendamento" as UC010
  usecase "UC-011\nConsultar agenda" as UC011
  usecase "UC-012\nConfigurar lembretes" as UC012
  usecase "UC-013\nEnviar lembretes" as UC013
  usecase "UC-014\nConsultar situação dos lembretes" as UC014
  usecase "UC-015\nConsultar registros de atendimento" as UC015
  usecase "UC-016\nConsultar auditoria" as UC016
  usecase "UC-017\nConsultar informações pessoais" as UC017
  usecase "Verificar disponibilidade" as Verificar
  usecase "Registrar operação de auditoria" as Auditar
}

Admin --> UC001
Admin --> UC002
Admin --> UC003
Admin --> UC005
Admin --> UC014
Admin --> UC016

Psi --> UC001
Psi --> UC004
Psi --> UC005
Psi --> UC006
Psi --> UC007
Psi --> UC008
Psi --> UC009
Psi --> UC010
Psi --> UC011
Psi --> UC012
Psi --> UC014
Psi --> UC015

Pac --> UC001
Pac --> UC017
Notif --> UC013

UC008 .> Verificar : <<include>>
UC009 .> Auditar : <<include>>
UC010 .> Auditar : <<include>>
UC013 .> Auditar : <<include>>

note right of Pac
O acesso direto do paciente
depende da confirmação
do escopo final do projeto.
end note
@enduml


' =========================================================
' 3. DIAGRAMA DE CLASSES
' =========================================================
@startuml
title 3 - Diagrama de Classes
skinparam classAttributeIconSize 0

abstract class Usuario {
  - idUsuario: int
  - nome: String
  - email: String
  - senhaHash: String
  - status: String
  + autenticar(): boolean
  + atualizarPerfil(): void
}

class Administrador {
  + gerenciarUsuarios(): void
  + gerenciarPermissoes(): void
  + consultarAuditoria(): void
}

class Psicologo {
  - registroProfissional: String
  - areaAtuacao: String
  + atualizarInformacoesProfissionais(): void
  + gerenciarDisponibilidade(): void
  + consultarAgenda(): void
  + confirmarAgendamento(): void
}

class Paciente {
  - idPaciente: int
  - nome: String
  - contato: String
  - status: String
  + atualizarCadastro(): void
  + consultarInformacoes(): void
}

class Disponibilidade {
  - idDisponibilidade: int
  - data: Date
  - horaInicio: Time
  - horaFim: Time
  - status: String
  + verificarDisponibilidade(): boolean
  + atualizarDisponibilidade(): void
}

class Agendamento {
  - idAgendamento: int
  - dataHora: DateTime
  - status: String
  + criar(): void
  + confirmar(): void
  + cancelar(): void
  + realizar(): void
  + verificarConflito(): boolean
}

class Lembrete {
  - idLembrete: int
  - antecedencia: String
  - canal: String
  - statusEnvio: String
  + configurar(): void
  + enviar(): void
  + registrarTentativa(): void
  + consultarSituacao(): String
}

class Atendimento {
  - idAtendimento: int
  - data: Date
  - registroConceitual: String
  + registrar(): void
  + consultar(): void
}

class RegistroAuditoria {
  - idAuditoria: int
  - operacao: String
  - dataHora: DateTime
  + registrarOperacao(): void
  + consultarRegistros(): void
}

Usuario <|-- Administrador
Usuario <|-- Psicologo

Psicologo "1" -- "0..*" Disponibilidade : cadastra
Psicologo "1" -- "0..*" Agendamento : atende
Paciente "1" -- "0..*" Agendamento : participa

Agendamento "1" *-- "0..*" Lembrete : possui
Agendamento "1" -- "0..1" Atendimento : origina

Usuario "1" -- "0..*" RegistroAuditoria : realiza

note bottom of Paciente
O vínculo entre psicólogo e paciente
deve ser representado no modelo
relacional por uma associação própria,
caso seja N:N.
end note
@enduml


' =========================================================
' 4. DIAGRAMA DE OBJETOS
' =========================================================
@startuml
title 4 - Diagrama de Objetos

object "psicologo01: Psicologo" as Psi {
  registroProfissional = "CRP-EXEMPLO"
  areaAtuacao = "Psicologia Clínica"
}

object "paciente01: Paciente" as Pac {
  idPaciente = 1
  nome = "Paciente Exemplo"
  contato = "exemplo@email.com"
  status = "Ativo"
}

object "disponibilidade01: Disponibilidade" as Disp {
  idDisponibilidade = 1
  data = "10/11/2026"
  horaInicio = "14:00"
  horaFim = "15:00"
  status = "Ocupado"
}

object "agendamento01: Agendamento" as Ag {
  idAgendamento = 1
  dataHora = "10/11/2026 14:00"
  status = "Confirmado"
}

object "lembrete01: Lembrete" as Lem {
  idLembrete = 1
  antecedencia = "24 horas"
  canal = "Email"
  statusEnvio = "Pendente"
}

Psi -- Disp
Psi -- Ag
Pac -- Ag
Ag -- Lem
@enduml


' =========================================================
' 5. DIAGRAMA DE SEQUÊNCIA
' =========================================================
@startuml
title 5A - Sequência UC-001: Realizar Login

actor Usuario
participant "Interface de Acesso" as Tela
participant "Serviço de Autenticação" as Auth
participant "Entidade Usuario" as EntidadeUsuario

Usuario -> Tela : informarCredenciais()
Tela -> Auth : autenticar(email, senha)
Auth -> EntidadeUsuario : consultarUsuario(email)
EntidadeUsuario --> Auth : dadosUsuario

alt Credenciais válidas e usuário ativo
  Auth --> Tela : autenticação aprovada
  Tela --> Usuario : acesso autorizado
else Credenciais inválidas ou usuário bloqueado
  Auth --> Tela : autenticação recusada
  Tela --> Usuario : apresentar mensagem de erro
end
@enduml

@startuml
title 5B - Sequência UC-008: Gerenciar Agendamento

actor Psicologo
participant "Interface da Agenda" as Tela
participant Disponibilidade
participant Agendamento
participant RegistroAuditoria as Auditoria

Psicologo -> Tela : informarPacienteEHorario()
Tela -> Agendamento : criar(dataHora, paciente)

Agendamento -> Disponibilidade : verificarDisponibilidade(dataHora)
Disponibilidade --> Agendamento : resultado

alt Horário disponível e sem conflito
  Agendamento -> Agendamento : criar()
  Agendamento -> Auditoria : registrarOperacao()
  Agendamento --> Tela : agendamento criado
  Tela --> Psicologo : apresentar confirmação
else Horário indisponível ou conflito
  Agendamento --> Tela : operação não permitida
  Tela --> Psicologo : informar indisponibilidade
end
@enduml

@startuml
title 5C - Sequência UC-013: Enviar Lembretes

participant Agendamento
participant Lembrete
participant "Serviço de Notificações" as Notificacao
participant RegistroAuditoria as Auditoria

Agendamento -> Lembrete : verificarLembretesPendentes()
Lembrete -> Notificacao : solicitarEnvio(destinatario, mensagem)

alt Envio realizado
  Notificacao --> Lembrete : envio concluído
  Lembrete -> Lembrete : registrarTentativa("Enviado")
else Falha no envio
  Notificacao --> Lembrete : falha de comunicação
  Lembrete -> Lembrete : registrarTentativa("Falha")
end

Lembrete -> Auditoria : registrarOperacao()
@enduml


' =========================================================
' 6. DIAGRAMA DE COMUNICAÇÃO
' =========================================================
@startuml
title 6 - Diagrama de Comunicação
left to right direction

object "psicologo: Psicologo" as Psi
object "disponibilidade: Disponibilidade" as Disp
object "agendamento: Agendamento" as Ag
object "paciente: Paciente" as Pac
object "auditoria: RegistroAuditoria" as Aud

Psi --> Ag : 1: criar(paciente, dataHora)
Ag --> Disp : 1.1: verificarDisponibilidade(dataHora)
Disp --> Ag : 1.2: retornarResultado()
Ag --> Ag : 1.3: verificarConflito()
Ag --> Aud : 1.4: registrarOperacao()
Ag --> Psi : 1.5: informarResultado()
Ag --> Pac : 1.6: disponibilizarInformacaoDoAgendamento()
@enduml


' =========================================================
' 7. DIAGRAMA DE ATIVIDADES
' =========================================================
@startuml
title 7 - Diagrama de Atividades

start

:Psicólogo acessa a agenda;
:Seleciona paciente;
:Informa data e horário;
:Consultar disponibilidade;

if (Horário disponível?) then (Sim)
  :Verificar conflito de agenda;

  if (Existe conflito?) then (Não)
    :Criar agendamento;
    :Registrar operação na auditoria;
    :Apresentar confirmação;
  else (Sim)
    :Informar conflito de horário;
  endif

else (Não)
  :Informar horário indisponível;
endif

stop
@enduml


' =========================================================
' 8. DIAGRAMA DE ESTADOS
' =========================================================
@startuml
title 8 - Diagrama de Estados do Agendamento

[*] --> AGENDADO : criar agendamento

AGENDADO --> CONFIRMADO : confirmar()
AGENDADO --> CANCELADO : cancelar()

CONFIRMADO --> CANCELADO : cancelar()
CONFIRMADO --> REALIZADO : realizar()

CANCELADO --> [*]
REALIZADO --> [*]
@enduml


' =========================================================
' 9. DIAGRAMA DE PACOTES
' =========================================================
@startuml
title 9 - Diagrama de Pacotes

package "Identidade e Acesso" {
  class Usuario
  class Administrador
}

package "Gestão de Profissionais" {
  class Psicologo
  class Disponibilidade
}

package "Gestão de Pacientes" {
  class Paciente
}

package "Agendamentos" {
  class Agendamento
  class Lembrete
}

package "Atendimentos" {
  class Atendimento
}

package "Auditoria" {
  class RegistroAuditoria
}

package "Persistência" {
  class Repositorio
}

"Identidade e Acesso" ..> "Persistência"
"Gestão de Profissionais" ..> "Persistência"
"Gestão de Pacientes" ..> "Persistência"
"Agendamentos" ..> "Persistência"
"Atendimentos" ..> "Persistência"
"Auditoria" ..> "Persistência"

"Agendamentos" ..> "Gestão de Profissionais"
"Agendamentos" ..> "Gestão de Pacientes"
"Agendamentos" ..> "Auditoria"
"Agendamentos" ..> "Atendimentos"
@enduml


' =========================================================
' 10. DIAGRAMA DE COMPONENTES
' =========================================================
@startuml
title 10 - Diagrama de Componentes

actor "Administrador Geral" as Admin
actor Psicólogo as Psi
actor "Cliente/Paciente" as Pac

component "Interface do Usuário" as UI
component "Autenticação e Controle de Acesso" as Auth
component "Gestão de Usuários e Permissões" as Usuarios
component "Gestão de Psicólogos e Pacientes" as Cadastros
component "Agenda e Disponibilidade" as Agenda
component "Gestão de Agendamentos" as Agendamentos
component "Gestão de Lembretes" as Lembretes
component "Gestão de Atendimentos" as Atendimentos
component "Auditoria" as Auditoria

database "Banco de Dados Relacional" as BD
component "Serviço de Notificações" as Notificacao

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
@enduml
