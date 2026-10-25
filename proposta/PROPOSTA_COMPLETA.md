# Proposta completa | Sentinelas do Commit

> **SIMULAÇÃO PREPARATÓRIA.** Não houve participação, consenso, voto no Mentimeter ou execução de software. As decisões abaixo são propostas para debate. Valores e horários pertencem à narrativa didática; não são prejuízos reais nem horários de commits.

**Integrantes:** João Victor Alves de Abreu (RM 564946) e Rodrigo Kenshin Viana Matayoshi (RM 564026).

## Premissas e estratégia

Proteger invariantes financeiras, corrigir a causa antes de silenciar alarmes e explicitar o custo de cada escolha. Na R2, privilegiamos a intervenção JDBC menor; na R4, assumimos uma dívida histórica visível. O grupo deve avaliar se esses custos são aceitáveis e pode escolher outro caminho.

O contrato integral da API está ausente: esta proposta usa somente regras explícitas das cartas. Nenhum código do banco foi fornecido, alterado ou testado.

**Placar de partida:** 🔥 7 · 💰 R$ 0 mil · 🧹 2.

## R1 - O saldo que virou negativo (19h12)

**Tipo:** rodada · **Voto proposto:** C

**Justificativa:** Remover o catch que engole SaldoInsuficienteException faz a rejeição interromper o fluxo antes do débito. Propor assertThrows e verificação de saldos inalterados protege a regra; aceitamos os 40 minutos adicionais e o custo simulado de R$ 35 mil para reduzir regressões.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1.

**Mecanismo e aula:** Exceções e fluxo de controle (Aula 11), com teste de comportamento (Aula 15).

**Consequência e limite:** A tabela prevê oito clientes afetados no total antes da correção. O teste não recupera os saldos já corrompidos.

