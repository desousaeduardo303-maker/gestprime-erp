 Estrutura do Documento de Requisitos
1. Objetivo do Módulo
Descreve em 2-3 linhas qual o propósito deste módulo.
Exemplo: "Gerir o ciclo de vida dos clientes da Elprime Solution, desde o registo inicial até à inativação, garantindo a integridade dos dados para faturação e contacto."


2. Atores (Quem usa?)
Quem vai interagir com este módulo?
Administrador: Acesso total.
Gestor de Vendas: Pode criar e editar, mas não apagar?
Visualizador: Apenas vê dados?
Pergunta para ti: Um estagiário pode apagar um cliente? (Pensa na segurança).
3. Requisitos Funcionais (O que o sistema faz?)


Lista as ações. Usa verbos no infinitivo.
RF01 - O sistema deve permitir registar um novo cliente.
RF02 - O sistema deve permitir listar clientes com paginação.
RF03 - O sistema deve permitir editar dados de um cliente existente.
RF04 - O sistema deve permitir inativar um cliente (soft delete).
RF05 - O sistema deve permitir pesquisar clientes por Nome, NIF ou Email.
Desafio: O que acontece se tentares registar um cliente com um NIF já existente? (Isso é uma regra de negócio).


4. Dicionário de Dados (O que guardamos?)
Aqui defines os campos. Sê específico.
Campo
Tipo
Obrigatório?
Validação / Regra
Nome
Texto (100)
Sim
Mínimo 3 caracteres
NIF
Número (9)
Sim
Único no sistema, formato válido
Email
Texto
Sim
Formato email, Único
Telefone
Texto
Não
Formato internacional (+244...)
Estado
Enum
Sim
Ativo, Inativo, Pendente
Pergunta Crítica: Um cliente pode ter mais que um telefone ou email? Se sim, como modelas isso? (Uma tabela separada ou um campo JSON? Justifica no documento).


5. Regras de Negócio (A lógica por trás)
Esta é a parte mais importante para um ERP.
RN01: Não é permitido apagar um cliente que tenha vendas associadas (integridade referencial). Deve-se apenas inativar.
RN02: O NIF deve ser validado antes de guardar (pesquisa algoritmos de validação de NIF).
RN03: O sistema deve impedir duplicação de Emails ativos.
Pergunta para ti: Como vamos lidar com clientes que são também fornecedores? Criamos duas tabelas ou uma só com um tipo? (Decide e justifica).


6. Requisitos Não Funcionais (Qualidade)
RNF01: A listagem de clientes deve carregar em menos de 2 segundos.
RNF02: O sistema deve ser responsivo (funcionar em tablet/telemóvel).
RNF03: Todos os dados sensíveis devem trafegar encriptados (HTTPS).




# Requisitos do Módulo de Gestão de Clientes - GestPrime ERP

## 1. Objetivo do Módulo
Gerir o ciclo de vida dos clientes da Elprime Solution, desde o registo inicial até à inativação, garantindo a integridade dos dados para faturação, contacto e relatórios comerciais.

## 2. Atores (Quem usa?)

| Ator | Permissões |
|------|------------|
| Administrador | CRUD completo (Criar, Ler, Atualizar, Apagar/Inativar) |
| Gestor de Vendas | Criar, Ler, Atualizar (Não pode apagar) |
| Visualizador | Apenas Ler (Listar e Ver detalhes) |

**Decisão:** Estagiários NÃO têm acesso a este módulo (apenas visualização se autorizado).

## 3. Requisitos Funcionais

| ID | Descrição | Prioridade |
|----|-----------|------------|
| RF01 | O sistema deve permitir registar um novo cliente | Alta |
| RF02 | O sistema deve permitir listar todos os clientes com paginação | Alta |
| RF03 | O sistema deve permitir editar dados de um cliente existente | Alta |
| RF04 | O sistema deve permitir inativar um cliente (Soft Delete) | Alta |
| RF05 | O sistema deve permitir pesquisar clientes por Nome, NIF ou Email | Alta |
| RF06 | O sistema deve validar NIF antes de guardar | Média |
| RF07 | O sistema deve impedir duplicação de NIF e Email | Alta |
| RF08 | O sistema deve permitir adicionar múltiplos contactos por cliente | Média |

## 4. Dicionário de Dados (Tabela: clients)

| Campo | Tipo | Obrigatório? | Validação / Regra |
|-------|------|--------------|-------------------|
| id | INT (PK) | Auto | Chave primária |
| nome | VARCHAR(150) | Sim | Mínimo 3 caracteres |
| nif | VARCHAR(20) | Sim | Único, formato válido |
| email | VARCHAR(100) | Sim | Formato email, Único |
| telefone | VARCHAR(20) | Não | Formato internacional (+244...) |
| morada | TEXT | Não | - |
| cidade | VARCHAR(50) | Não | - |
| pais | VARCHAR(50) | Sim | Default: Angola |
| estado | ENUM | Sim | Ativo, Inativo, Pendente |
| created_at | TIMESTAMP | Auto | - |
| updated_at | TIMESTAMP | Auto | - |
| deleted_at | TIMESTAMP | Null | Soft delete |

**Decisão de Modelagem:** 
- Optei por uma tabela separada `client_contacts` para múltiplos contactos (justificação: um cliente pode ter vários telefones/emails sem duplicar dados na tabela principal).

## 5. Regras de Negócio

| ID | Regra | Justificação |
|----|-------|--------------|
| RN01 | Não é permitido apagar um cliente com vendas associadas | Integridade referencial e histórica |
| RN02 | O NIF deve ser único no sistema | Evitar duplicação de entidades fiscais |
| RN03 | O Email deve ser único entre clientes ativos | Garantir comunicação única |
| RN04 | Cliente inativo não pode ser associado a novas vendas | Evitar vendas a clientes bloqueados |
| RN05 | Soft Delete é obrigatório (nunca DELETE físico) | Manter histórico para relatórios e auditoria |

## 6. Requisitos Não Funcionais

| ID | Requisito |
|----|-----------|
| RNF01 | A listagem de clientes deve carregar em menos de 2 segundos |
| RNF02 | O sistema deve ser responsivo (mobile, tablet, desktop) |
| RNF03 | Todos os dados devem trafegar encriptados (HTTPS) |
| RNF04 | A API deve retornar erros em formato JSON consistente |

## 7. Decisões de Arquitetura

### Cliente vs Fornecedor vs Colaborador
**Decisão:** Tabelas separadas para cada entidade.
**Justificação:** Embora partilhem dados semelhantes, cada um tem regras de negócio e atributos específicos. Exemplo: Colaborador tem dados de RH, Fornecedor tem dados de compras. Manter separado evita complexidade desnecessária.

### Soft Delete
**Decisão:** Usar campo `deleted_at` (padrão Laravel).
**Justificação:** Permite restaurar registos acidentalmente removidos e mantém histórico para auditoria.

---

**Autor:** Pedro Raimundo  
**Data:** [Inserir Data]  
**Versão:** 1.0