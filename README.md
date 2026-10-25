# Ficha de Decisões | Sentinelas do Commit

**Checkpoint 6 - War Room FiapBank | Programação Orientada a Objetos**

| Integrante | RM |
|---|---|
| João Victor Alves de Abreu | 564946 |
| Rodrigo Kenshin Viana Matayoshi | 564026 |

> **Escopo:** ficha do percurso proposto para o grupo. As letras e os placares abaixo descrevem a resolução elaborada a partir das cartas; não comprovam debate, voto no Mentimeter ou apresentação. O template oficial `FICHA_DECISOES_GRUPO.md` do Teams ainda precisa ser conferido antes da entrega final.

## Dossiê técnico - síntese das regras disponíveis

Esta síntese reúne somente regras explícitas nas cartas públicas. Ela não substitui o dossiê integral do template do Teams: limites, campos obrigatórios, contratos de erro e demais regras não recebidas permanecem por conferir.

| Tema | Regra ou evidência disponível | Origem |
|---|---|---|
| Transferência e saldo | A origem deve ter saldo para valor + taxa; a rejeição por saldo insuficiente deve impedir o débito | R1 |
| Busca de contas | Busca por titular; o repository já oferece findByTitular(String titular). A entrada externa não deve compor a sintaxe SQL | R2 |
| Número da conta | A carta exige identificador único e descreve a promessa de numeração sequencial. O gerador fornecido recria a instância | R3 |
| Comprovante | Origem e destino do registro devem corresponder ao sentido da transferência; os saldos da evidência já estão corretos | R4 |
| Tipos de conta | A factory apresentada cria CORRENTE e POUPANCA; UNIVERSITARIA cai no default que retorna null | R5 |
| Construção da conta | Defaults e validações devem preservar as exigências do contrato; a obrigatoriedade integral depende do template | Relâmpago C |
| Depósito | No caso apresentado, saldo 100 com depósito 50 deve resultar em 150 | Relâmpago D |
| Taxa da TED | O contrato citado na carta é R$ 5; o literal de R$ 50 é o defeito a investigar | R6 |

Os caminhos `/api/transferencias`, `/api/contas` e `/api/contas/titular/{nome}` aparecem nas evidências. Não foi fornecida a especificação completa da API nem um projeto executável do banco.

## Estratégia e placar inicial

Proteger regras financeiras e explicitar o custo de cada decisão. O percurso combina contenção por JDBC na R2 e correção restrita dos comprovantes na R4; essas escolhas têm custos e são revisitadas no pós-mortem.

**Placar inicial:** 🔥 7 · 💰 R$ 0 mil · 🧹 2.

Reputação é expressa em pontos, prejuízo em milhares de reais e dívida técnica em unidades da dinâmica. Horários são os da narrativa, não datas de commits. Os resultados das cartas são consequências do cenário; não representam uma aplicação corrigida, uma suíte executada ou valores financeiros reais.

## R1 - O saldo que virou negativo (19h12)

**Tipo:** rodada · **Voto:** C

**Justificativa:** Remover o catch que engole SaldoInsuficienteException interrompe o fluxo antes do débito; assertThrows e saldos inalterados verificam a rejeição. Aceitamos 40 minutos adicionais e R$ 35 mil no cenário para reduzir regressões.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1.

**Mecanismo e aula:** Exceções e fluxo de controle (Aula 11), com teste de comportamento (Aula 15).

**Consequência e limite:** A tabela prevê oito clientes afetados no total antes da correção. O teste não recupera os saldos já corrompidos.

## R2 - A injeção que derreteu o banco (21h02)

**Tipo:** rodada · **Voto:** A

**Justificativa:** PreparedStatement com WHERE titular = ? e setString separa o valor da estrutura SQL. Aceitamos manter JDBC manual para conter a injeção em cerca de 40 minutos, dez a menos que JPA, assumindo a revisão do ciclo de vida dos recursos.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1.

**Mecanismo e aula:** JDBC, parâmetros vinculados e SQL injection (Aula 12).

**Consequência e limite:** A opção D também é tecnicamente forte e reutiliza findByTitular; escolher A preserva um caminho manual que exige revisão dos recursos.

## Evento relâmpago B - As conexões que não fecham (22h40)

**Tipo:** relâmpago · **Voto:** A

**Justificativa:** Try-with-resources fecha Connection, Statement e ResultSet também na falha, liberando a conexão; quando há pool, o fechamento a devolve. Aceitamos cerca de 30 minutos para revisar os três métodos; reiniciar ou aumentar o pool apenas adia o vazamento.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1.

**Mecanismo e aula:** Gerenciamento de recursos JDBC (Aula 12).

**Consequência e limite:** Aplicável porque R2 = A. A aquisição da conexão deve respeitar o DataSource e o gerenciamento transacional da aplicação; não criar um pool paralelo.

## R3 - A noite das contas gêmeas (22h50)

**Tipo:** rodada · **Voto:** D

**Justificativa:** A sequence do banco coordena a numeração entre instâncias e remove a dependência de um Singleton em memória. Aceitamos uma hora de migração e R$ 10 mil; unicidade e reconciliação de duplicatas antigas continuam necessárias, e a sequence pode deixar lacunas.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 45 mil · 🧹 1.

**Mecanismo e aula:** Singleton e responsabilidade pela criação (Aula 14), persistência (Aula 13).

**Consequência e limite:** Sequence não corrige extratos cruzados antigos nem promete numeração sem lacunas. A exigência contratual exata deve ser conferida no dossiê.

## R4 - O comprovante que aponta pra pessoa errada (00h47)

**Tipo:** rodada · **Voto:** A

**Justificativa:** Corrigir a ordem de origem e destino na entidade protege novos comprovantes sem repetir a movimentação dos saldos, que já estão certos. Aceitamos R$ 15 mil e uma dívida de dados: o histórico antigo segue invertido e exige reconciliação prioritária.

**Placar do grupo após esta decisão:** 🔥 9 · 💰 R$ 60 mil · 🧹 2.

**Mecanismo e aula:** Mapeamento e persistência de entidades (Aula 13).

**Consequência e limite:** Não alegar integridade histórica total: comprovantes já emitidos continuam errados. A correção de dados é prioridade antes de considerar o incidente encerrado.

## R5 - A fábrica que devolve null (01h30)

**Tipo:** rodada · **Voto:** A

**Justificativa:** Lançar uma exceção clara na factory impede que null chegue ao save e esconda a causa. Aceitamos rejeitar UNIVERSITARIA e perder um ponto de reputação; fail-fast melhora o diagnóstico, mas ainda não entrega o produto anunciado.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 60 mil · 🧹 2.

**Mecanismo e aula:** Polimorfismo, exceções e Factory (Aulas 8, 11 e 14).

**Consequência e limite:** Mapear a exceção para uma resposta compreensível é trabalho proposto; o status HTTP exato depende do contrato não recebido.

## Evento relâmpago C - O cadastro com metade dos campos (02h20)

**Tipo:** relâmpago · **Voto:** B

**Justificativa:** Builder concentra campos nomeados, defaults permitidos e validação no build(), evitando construtores telescópicos. Aceitamos o esforço adicional de construção explícita; campos obrigatórios no contrato não podem receber valores inventados para viabilizar a campanha.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 60 mil · 🧹 2.

**Mecanismo e aula:** Builder e construção válida de objetos (Aula 14).

**Consequência e limite:** Aplicável porque R5 = A. Se o dossiê exigir CPF e tipo, rejeitar a construção incompleta ou propor um cadastro preliminar separado, sem tratá-lo como conta válida.

## Evento relâmpago D - O teste verde que não testa nada (03h20)

**Tipo:** relâmpago · **Voto:** A

**Justificativa:** assertEquals verifica saldo final de 150 no exemplo de depósito de 50 sobre 100 e faz o teste detectar ausência do crédito. Aceitamos dez minutos de revisão para substituir um verde sem verificação por evidência de comportamento.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 60 mil · 🧹 1.

**Mecanismo e aula:** Testes unitários e asserções (Aula 15).

**Consequência e limite:** Aplicável porque R2 = A. O saldo de 150 é o exemplo desta carta; nenhuma suíte real foi executada.

## R6 - O teste vermelho às 03h45 (03h45)

**Tipo:** rodada · **Voto:** B

**Justificativa:** Investigar preserva o contrato TED = R$ 5 e identifica o literal incorreto de R$ 50, sem mudar o teste para confirmar o bug. Aceitamos perder a janela, R$ 30 mil e um ponto de reputação; a correção precisa passar pela suíte antes da implantação.

**Placar do grupo após esta decisão:** 🔥 7 · 💰 R$ 90 mil · 🧹 0.

**Mecanismo e aula:** Contrato, regressão e testes unitários (Aula 15).

**Consequência e limite:** Estado SAUDÁVEL para a regra do jogo. A própria carta diz que as correções só entram depois da abertura; isso exige cautela operacional no Go/No-Go.

## R7 - Go/No-Go da Black Friday (06h15)

**Tipo:** rodada · **Voto:** A

**Justificativa:** Na tabela didática, R6 = B classifica a suíte como saudável e sustenta a abertura no horário. Aceitamos uma unidade de dívida e a manutenção posterior do serviço; fora dessa regra narrativa, o Go depende de confirmar a versão corrigida em produção.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 90 mil · 🧹 1.

**Mecanismo e aula:** Coesão, risco de implantação e evidência de testes (Aulas 13 a 15).

**Consequência e limite:** A tabela saudável prevê abertura no horário e estabilidade no pico; isso é consequência do jogo, não resultado de teste de carga. Se as correções estiverem pendentes, reconsiderar B ou C com o grupo.

## Relâmpagos fora deste percurso

- A não se aplica: R1 foi C, não D. Não há voto nem efeito a somar.
- E não se aplica: R6 foi B, logo a suíte é saudável segundo a carta. Não há voto nem efeito a somar.
- B, C e D se aplicam, respectivamente, por R2 = A, R5 = A e R2 = A. Foram registrados no ponto correto da cronologia.

## Pós-mortem

### Resumo executivo e perfil final

O percurso proposto termina com **🔥 8 · 💰 R$ 90 mil · 🧹 1**, perfil **A Noite dos Heróis** (reputação pelo menos 7 e dívida até 2). Não se aplicam os perfis adicionais de prejuízo de 400 mil ou dívida de 6. Esse perfil é uma classificação narrativa; não equivale à nota da avaliação.

A principal tensão foi preservar o contrato da TED e os testes, mesmo perdendo a janela na R6. A abertura A da R7 segue a tabela saudável, mas não elimina a dívida dos comprovantes antigos nem comprova que todas as correções estão implantadas. O risco residual deve ser declarado ao professor e revisto pelo grupo.

### Linha do tempo e caminho escolhido

| Horário narrativo | Incidente e escolha no percurso | Efeito relevante | Placar após (🔥 / R$ mil / 🧹) |
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

### Plano de ações

Os responsáveis abaixo são uma divisão sugerida, ainda sujeita ao acordo dos integrantes. Prazos são relativos à recuperação na narrativa.

| Ação | Responsável sugerido | Prioridade/prazo | Critério de conclusão |
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

## Materiais de apoio e conferência da entrega

- [Roteiro da apresentação de 2 a 3 minutos](proposta/ROTEIRO_APRESENTACAO.md)
- [Placar passo a passo em JSON](proposta/placar.json)
- [Critérios, pendências e procedimento de entrega](docs/ENTREGA_E_CRITERIOS.md)
- [Fontes consultadas](docs/FONTES.md)
- [Resultado da revisão final](docs/REVISAO_FINAL.md)

**Entrega no Teams:** enviar somente o link deste repositório, conforme o PDF. A postagem e a apresentação ainda não foram realizadas nesta preparação.
