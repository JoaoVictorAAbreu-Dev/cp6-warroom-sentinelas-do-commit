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

## Evento relâmpago D - O teste verde que não testa nada (03h20)

**Tipo:** relâmpago · **Voto proposto:** A

**Justificativa:** Adicionar assertEquals para conferir saldo final de 150 após depósito de 50 sobre 100 verifica o comportamento que o teste prometia. Aceitamos os dez minutos de revisão e incluímos a rejeição de depósitos inválidos conforme o contrato; executar sem assert não comprova o crédito.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 60 mil · 🧹 1.

**Mecanismo e aula:** Testes unitários e asserções (Aula 15).

**Consequência e limite:** Aplicável porque R2 = A. O saldo de 150 é o exemplo desta carta; nenhuma suíte real foi executada.

## R6 - O teste vermelho às 03h45 (03h45)

**Tipo:** rodada · **Voto proposto:** B

**Justificativa:** Investigar a divergência preserva o contrato TED = R$ 5 e revela o literal incorreto de R$ 50 no serviço, em vez de normalizar o defeito no teste. Aceitamos perder a janela, R$ 30 mil simulados e um ponto de reputação; a correção deve manter a asserção e passar pela suíte antes da implantação.

**Placar simulado após esta decisão:** 🔥 7 · 💰 R$ 90 mil · 🧹 0.

**Mecanismo e aula:** Contrato, regressão e testes unitários (Aula 15).

**Consequência e limite:** Estado SAUDÁVEL para a regra do jogo. A própria carta diz que as correções só entram depois da abertura; isso exige cautela operacional no Go/No-Go.

## R7 - Go/No-Go da Black Friday (06h15)

**Tipo:** rodada · **Voto proposto:** A

**Justificativa:** Pela classificação saudável obtida em R6 = B, propomos abrir no horário preservando o teste da taxa e evitando mais indisponibilidade. Aceitamos uma unidade de dívida técnica e a manutenção posterior do serviço concentrado; uma implantação real exigiria confirmar a versão corrigida em produção, o que esta simulação não comprova.

**Placar simulado após esta decisão:** 🔥 8 · 💰 R$ 90 mil · 🧹 1.

**Mecanismo e aula:** Coesão, risco de implantação e evidência de testes (Aulas 13 a 15).

**Consequência e limite:** A tabela saudável prevê abertura no horário e estabilidade no pico; isso é consequência do jogo, não resultado de teste de carga. Se as correções estiverem pendentes, reconsiderar B ou C com o grupo.

## Relâmpagos fora deste percurso

- A não se aplica: R1 foi C, não D. Não há voto nem efeito a somar.
- E não se aplica: R6 foi B, logo a suíte é saudável segundo a carta. Não há voto nem efeito a somar.
- B, C e D se aplicam, respectivamente, por R2 = A, R5 = A e R2 = A. Foram registrados no ponto correto da cronologia.

## Pós-mortem simulado

### Resumo executivo e perfil final

O percurso proposto termina com **🔥 8 · 💰 R$ 90 mil · 🧹 1**, perfil **A Noite dos Heróis** (reputação pelo menos 7 e dívida até 2). Não se aplicam os perfis adicionais de prejuízo de 400 mil ou dívida de 6. Esse perfil é uma classificação narrativa; não equivale à nota da avaliação.

A principal tensão foi preservar o contrato da TED e os testes, mesmo perdendo a janela na R6. A abertura A da R7 segue a tabela saudável, mas não elimina a dívida dos comprovantes antigos nem comprova que todas as correções estão implantadas. O risco residual deve ser declarado ao professor e revisto pelo grupo.

### Linha do tempo e caminho escolhido

| Horário narrativo | Incidente e proposta | Efeito relevante | Placar após (🔥 / R$ mil / 🧹) |
|---|---|---|---|
| 19h12 | R1 = C | Δ +1 / +35 / -1 | 8 / 35 / 1 |
| 21h02 | R2 = A | Δ +0 / +0 / +0 | 8 / 35 / 1 |
| 22h40 | Evento relâmpago B = A | Δ +0 / +0 / +0 | 8 / 35 / 1 |
| 22h50 | R3 = D | Δ +0 / +10 / +0 | 8 / 45 / 1 |
| 00h47 | R4 = A | Δ +1 / +15 / +1 | 9 / 60 / 2 |
| 01h30 | R5 = A | Δ -1 / +0 / +0 | 8 / 60 / 2 |
| 02h20 | Evento relâmpago C = B | Δ +0 / +0 / +0 | 8 / 60 / 2 |
| 03h20 | Evento relâmpago D = A | Δ +0 / +0 / -1 | 8 / 60 / 1 |
| 03h45 | R6 = B | Δ -1 / +30 / -1 | 7 / 90 / 0 |
| 06h15 | R7 = A | Δ +1 / +0 / +1 | 8 / 90 / 1 |

O prejuízo acumulado é 35 + 10 + 15 + 30 = **90 mil**, sem custos atribuídos às demais decisões nesta tabela. O saldo de dívida resulta de 2 - 1 + 1 - 1 - 1 + 1 = **1**. Reputação: 7 + 1 + 1 - 1 - 1 + 1 = **8**. Não houve aplicação de piso ou teto artificial.

### Causa raiz 1 - saldo negativo (R1)

**Evidência:** a exceção de saldo insuficiente é capturada e ignorada dentro do mesmo método; o débito permanece depois do catch. A chamada retorna sucesso mesmo diante da violação observada.

**Mecanismo:** o catch transforma uma rejeição do domínio em continuação normal do fluxo. A causa não é a existência de uma exceção, mas sua neutralização antes da mutação do saldo. Conceitos: exceções da Aula 11 e asserções da Aula 15.

**Correção proposta:** permitir que a rejeição interrompa o método antes de qualquer mutação. Testar saldo inferior ao total, saldo exatamente igual a valor + taxa e verificar que origem, destino e registro de transferência não mudam quando a operação é rejeitada. AssertThrows isolado não detecta uma mutação realizada antes do lançamento.

**Limite:** o trecho fornecido não demonstra atomicidade ou proteção concorrente. Revisar transação e concorrência seria uma ação adicional; não declarar esse problema resolvido por um teste unitário nem atribuí-lo como causa comprovada da carta. Reconciliar clientes já afetados separadamente.

### Causa raiz 2 - injeção SQL (R2)

**Evidência:** o nome recebido na URL é concatenado no SQL, e o log mostra um predicado OR que retorna todas as contas.

**Mecanismo:** dados externos passam a compor a sintaxe de uma consulta executada por Statement. A validação por blocklist não estabelece a separação entre dado e comando. Conceitos: JDBC (Aula 12) e alternativa de repository (Aula 13).

**Correção proposta:** parametrizar o valor com PreparedStatement e revisar os três métodos com try-with-resources. Cobrir busca legítima e entrada maliciosa como valor literal; em teste de integração, verificar retorno e ausência de vazamento sob repetição e exceção.

**Consequência no caminho:** ao escolher o hotfix JDBC, o cenário aciona o relâmpago B: a correção apressada vazou recursos. A ação B = A responde a essa segunda falha; uma revisão de segurança precisa incluir ciclo de vida, não apenas a consulta.

**Limite:** a parametrização não comprova autorização de acesso nem restaura eventual exposição anterior. Nenhum teste de integração foi executado nesta preparação.

### O que faríamos diferente em duas rodadas

**R2: de A para D.** Reutilizar findByTitular já existente alinha a busca à arquitetura e reduz gestão manual de recursos. O custo é a estimativa de 50 minutos em vez de 40. Pela carta, evita B e D e reduz uma unidade de dívida na R2. A motivação é reduzir causas recorrentes, mesmo pagando mais tempo na primeira intervenção.

**R4: de A para B.** Corrigir a gravação e reprocessar o histórico evita manter comprovantes contraditórios para clientes e auditoria. Aceitaríamos cerca de duas horas e R$ 25 mil, contra 30 minutos e R$ 15 mil na A. A migração de dados precisaria de rastreabilidade e validação para não trocar saldos ou inverter registros duas vezes. São alternativas retrospectivas; o placar congelado acima permanece o do percurso original. Não somar efeitos de escolhas incompatíveis.

### Plano de ações proposto

Os responsáveis abaixo são uma divisão sugerida, ainda sujeita ao acordo dos integrantes. Prazos são relativos à recuperação na narrativa.

| Ação | Responsável proposto | Prioridade/prazo | Critério de conclusão |
|---|---|---|---|
| Revisar rejeição por saldo e efeitos colaterais | João Victor | Antes de liberar o fluxo corrigido | Rejeição e saldos inalterados verificados; transação revisada |
| Revisar parâmetros e fechamento dos três métodos JDBC | Rodrigo | Antes de liberar a busca | Entrada é dado; recursos liberados também na falha |
| Auditar duplicatas e sincronizar sequence com dados existentes | Rodrigo | Antes de reabrir cadastro afetado | Identificadores sem colisão; vínculos antigos reconciliados |
| Corrigir comprovantes históricos deixados pela R4 | João Victor | Prioridade após contenção; não encerrar antes de validar | Origem/destino conferidos sem nova movimentação financeira |
| Fixar taxa TED no contrato e executar regressões | Ambos | Antes da implantação | Teste de R$ 5 passa sem Disabled nem mudança oportunista de expectativa |
| Revisar responsabilidades do TransferenciaService | Ambos | Planejar após estabilização | Validação, criação e persistência com responsabilidades claras, sem reescrita ampla na emergência |

### Go/No-Go operacional e evidência

A carta R6 = B classifica a suíte como saudável, mas também afirma que as correções entram após a abertura. Por isso, a proposta R7 = A é defensável apenas dentro da tabela didática. Para uma liberação real, conferir commit implantado, teste da TED, saldo e extrato, migração e métricas de produção é condição de operação. Se isso não estiver disponível, o grupo deve discutir R7 = B (corrigir e abrir às 10h) em vez de usar o selo de suíte saudável como prova de deploy.

### Maior lição

Um teste vermelho pode proteger o contrato; um verde sem asserção pode escondê-lo. A pressa também troca uma falha por outra, como a parametrização sem fechamento de recursos. Aceitar uma dívida exige registrá-la e definir como encerrá-la.

**Palavra da madrugada: Responsabilidade.**
