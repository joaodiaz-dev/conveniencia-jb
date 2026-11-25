# 🛒 Banco de Dados da Mercearia

Projeto de banco de dados relacional em MySQL para controlar produtos, estoque, clientes e vendas de uma mercearia.

[Sobre](#sobre) [Tabelas](#tabelas) [Relacionamentos](#relacionamentos) [Consultas](#consultas) [Como executar](#executar)

## Sobre o projeto

A mercearia precisa saber **o que vende**, **quanto tem em estoque** e **quem comprou**. O banco foi modelado com 7 tabelas ligadas por chaves estrangeiras, usando restrições como `PRIMARY KEY`, `UNIQUE`, `NOT NULL`, `DEFAULT` e `CHECK` para manter os dados consistentes.

## Tabelas

### categoria

- id_categoria (PK)
- nome (único)

### fornecedor

- id_fornecedor (PK)
- nome
- cnpj (único)
- telefone

### produto

- id_produto (PK)
- nome, unidade
- preco_venda (> 0)
- estoque (≥ 0)
- id_categoria (FK)
- id_fornecedor (FK)

### cliente

- id_cliente (PK)
- nome
- cpf (único)
- telefone

### funcionario

- id_funcionario (PK)
- nome, cargo
- data_admissao

### venda

- id_venda (PK)
- data_venda
- forma_pagamento
- id_cliente (FK, opcional)
- id_funcionario (FK)

### item_venda

- id_venda + id_produto (PK composta)
- quantidade (> 0)
- preco_unitario

## Relacionamentos

| Tabela | Relação | Tabela |
| --- | --- | --- |
| categoria | 1 : N | produto |
| fornecedor | 1 : N | produto |
| cliente | 1 : N | venda |
| funcionario | 1 : N | venda |
| venda | 1 : N | item_venda |
| produto | 1 : N | item_venda |

A tabela `item_venda` resolve a relação muitos-para-muitos entre `venda` e `produto`.

## Exemplos de consultas

### Valor total de cada venda

```
SELECT v.id_venda, v.data_venda, v.forma_pagamento,
       SUM(i.quantidade * i.preco_unitario) AS total
FROM venda v
JOIN item_venda i ON i.id_venda = v.id_venda
GROUP BY v.id_venda, v.data_venda, v.forma_pagamento;
```

### Produtos com estoque baixo

```
SELECT nome, estoque
FROM produto
WHERE estoque < 10
ORDER BY estoque;
```

### Produtos mais vendidos

```
SELECT p.nome, SUM(i.quantidade) AS total_vendido
FROM item_venda i
JOIN produto p ON p.id_produto = i.id_produto
GROUP BY p.nome
ORDER BY total_vendido DESC;
```

## Como executar

1. Abra o MySQL Workbench ou o terminal do MySQL.
2. Abra o arquivo `mercearia.sql`.
3. Execute o script inteiro: ele cria o banco, as tabelas, insere os dados, faz as consultas e cria a view `vw_resumo_vendas`.

Projeto de Banco de Dados Relacional · MySQL