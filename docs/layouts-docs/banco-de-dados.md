# Documentação do Banco de Dados PostgreSQL

## Visão Geral

Este documento apresenta a estrutura do banco de dados PostgreSQL utilizado no projeto, incluindo tabelas, relacionamentos, índices e outras informações importantes para o entendimento e manutenção da base de dados.

O banco de dados foi projetado para atender aos requisitos do sistema com foco em performance, integridade e escalabilidade.

---

## Estrutura do Banco de Dados

- **SGBD:** PostgreSQL
- **Versão:** 
- **Principais tabelas:** 
- **Relacionamentos:** Chaves estrangeiras e índices para otimização
- **Esquema:** público (public)

---

## Documentação Detalhada Gerada pelo SchemaSpy

Para facilitar a navegação e análise da estrutura do banco, foi gerada uma documentação completa utilizando o [SchemaSpy](http://schemaspy.org/){:target="_blank"}.

Você pode acessar essa documentação diretamente pelo link abaixo:

[🔗 Visualizar Documentação do SchemaSpy](caminho/para/doc){:target="_blank"}

---

# Como gerar a documentação no SchemaSpy

## Instalações e pré-requisitos

Primeiro passo é a instação do JDK, utilizando o executável que está nesse repositório de documentações.

Após isso, executar o comando abaixo, com as credenciais do banco de dados utilizado.
Lembre-se: o comando deve ser executado dentro da pasta desse  repositório de documentações.

```powershell
java -jar schemaspy.jar `
  -t pgsql `
  -dp "postgresql-42.7.7.jar" `
  -host localhost `
  -port 5432 `
  -db nome-do-db `
  -u usuario `
  -p "senha" `
  -o ./docs/banco-de-dados/documentacao `
  -s public `
  -renderer :cairo `
  -imageformat png `
  -vizjs
```
