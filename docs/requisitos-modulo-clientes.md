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
