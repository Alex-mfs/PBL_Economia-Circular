# Base de Dados — TáNaValidade

Modelo relacional desenvolvido em **MySQL Workbench**, no âmbito do projeto PBL de Economia Circular.

## Estrutura da base de dados

A base de dados contém **19 tabelas**, destinadas à gestão de:

- Utilizadores e autenticação;
- Habitações e respetiva partilha entre utilizadores;
- Produtos, alimentos registados e locais de armazenamento;
- Datas de validade e quantidades disponíveis;
- Consumo, desperdício e ajustes de quantidades dos alimentos;
- Receitas, ingredientes, fotografias e comentários;
- Gamificação, pontos, rankings e medalhas;
- Notificações e preferências dos utilizadores.

## Atualização do modelo

Foi acrescentada a tabela `movimento_alimento`, relacionada com `utilizador` e `alimento_registado`.

Esta tabela permite registar a utilização de alimentos, o desperdício e os ajustes de quantidade, contribuindo para um melhor acompanhamento do aproveitamento alimentar.

## Ferramentas

- MySQL Workbench
- MySQL
- Modelo relacional com chaves primárias e estrangeiras

## Objetivo

Organizar a informação necessária ao funcionamento do TáNaValidade, uma aplicação que pretende ajudar os utilizadores a controlar a validade dos alimentos, incentivar o seu aproveitamento e reduzir o desperdício alimentar.
