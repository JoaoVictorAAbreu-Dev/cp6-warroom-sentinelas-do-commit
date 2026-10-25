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

## R2 - A injeção que derreteu o banco (21h02)

**Tipo:** rodada · **Voto proposto:** A

**Justificativa:** PreparedStatement com WHERE titular = ? e setString separa o valor recebido da estrutura SQL e elimina a concatenação vulnerável. Aceitamos manter JDBC manual para a correção estimada em 40 minutos, dez a menos que a migração JPA; fechamento de recursos passa a ser uma obrigação explícita.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1.

**Mecanismo e aula:** JDBC, parâmetros vinculados e SQL injection (Aula 12).

**Consequência e limite:** A opção D também é tecnicamente forte e reutiliza findByTitular; escolher A preserva um caminho manual que exige revisão dos recursos.

## Evento relâmpago B - As conexões que não fecham (22h40)

**Tipo:** relâmpago · **Voto proposto:** A

**Justificativa:** Try-with-resources fecha ResultSet, PreparedStatement e Connection mesmo quando ocorre exceção, devolvendo a conexão ao pool. Aceitamos cerca de 30 minutos para corrigir os três métodos; aumentar o pool ou reiniciar apenas adiaria a exaustão.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1.

**Mecanismo e aula:** Gerenciamento de recursos JDBC (Aula 12).

**Consequência e limite:** Aplicável porque R2 = A. A aquisição da conexão deve respeitar o DataSource e o gerenciamento transacional da aplicação; não criar um pool paralelo.

## R3 - A noite das contas gêmeas (22h50)

**Tipo:** rodada · **Voto proposto:** D

**Justificativa:** Delegar a geração à sequence do banco remove a dependência de um Singleton em memória e coordena identificadores entre instâncias da aplicação. Aceitamos uma hora de migração e R$ 10 mil simulados; impor unicidade e reconciliar duplicatas antigas continua necessário, e sequence pode deixar lacunas.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 45 mil · 🧹 1.

**Mecanismo e aula:** Singleton e responsabilidade pela criação (Aula 14), persistência (Aula 13).

**Consequência e limite:** Sequence não corrige extratos cruzados antigos nem promete numeração sem lacunas. A exigência contratual exata deve ser conferida no dossiê.

## R4 - O comprovante que aponta pra pessoa errada (00h47)

**Tipo:** rodada · **Voto proposto:** A

**Justificativa:** Gravar origem e destino na ordem correta do construtor corrige os novos comprovantes sem movimentar novamente os saldos, que já estão certos. Aceitamos manter o histórico antigo invertido para reduzir o escopo imediato; a reconciliação dos registros anteriores fica como dívida de dados com responsável e validação.

**Placar simulado após esta decisão:** 🔥 9 · 💰 R$ 60 mil · 🧹 2.

**Mecanismo e aula:** Mapeamento e persistência de entidades (Aula 13).

**Consequência e limite:** Não alegar integridade histórica total: comprovantes já emitidos continuam errados. A correção de dados é prioridade antes de considerar o incidente encerrado.

## R5 - A fábrica que devolve null (01h30)

**Tipo:** rodada · **Voto proposto:** A

**Justificativa:** A factory deve lançar uma exceção explícita para tipo não suportado, impedindo que null chegue ao save e esconda a origem da falha. Aceitamos rejeitar UNIVERSITARIA e perder um ponto de reputação no cenário; fail-fast melhora o diagnóstico, mas não implementa o produto anunciado.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 60 mil · 🧹 2.

**Mecanismo e aula:** Polimorfismo, exceções e Factory (Aulas 8, 11 e 14).

**Consequência e limite:** Mapear a exceção para uma resposta compreensível é trabalho proposto; o status HTTP exato depende do contrato não recebido.

## Evento relâmpago C - O cadastro com metade dos campos (02h20)

**Tipo:** relâmpago · **Voto proposto:** B

**Justificativa:** Builder organiza a construção com campos nomeados, concentra defaults permitidos e valida invariantes no build(), evitando construtores telescópicos. Aceitamos o esforço adicional de construção explícita; CPF e tipo obrigatórios não podem ser inventados nem omitidos para satisfazer a campanha.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 60 mil · 🧹 2.

**Mecanismo e aula:** Builder e construção válida de objetos (Aula 14).

**Consequência e limite:** Aplicável porque R5 = A. Se o dossiê exigir CPF e tipo, rejeitar a construção incompleta ou propor um cadastro preliminar separado, sem tratá-lo como conta válida.

