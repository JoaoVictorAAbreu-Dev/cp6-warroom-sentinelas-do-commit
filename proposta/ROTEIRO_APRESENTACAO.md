# A madrugada dos Sentinelas do Commit

**Roteiro de ensaio para o cenário proposto; adaptar ao caminho real antes de apresentar.** Estimativa: 2 a 3 minutos, a confirmar em ensaio cronometrado. Não foi gravado áudio ou vídeo.

## João Victor | abertura e primeira noite

Somos os Sentinelas do Commit: João Victor Alves de Abreu, RM 564946, e Rodrigo Kenshin Viana Matayoshi, RM 564026. Nosso princípio neste percurso foi proteger as regras financeiras e tornar o preço de cada decisão visível.

Na primeira rodada, o sistema lançava uma exceção por saldo insuficiente e a engolia no mesmo método. O débito continuava. Propomos C: corrigir o fluxo e testar a rejeição, incluindo a ausência de mudança nos saldos. Isso custa tempo, mas impede que o teste deixe a mesma falha passar.

Na busca, escolhemos o hotfix com PreparedStatement para separar dado de comando SQL. A pressa trouxe outra consequência: conexões sem fechamento. No relâmpago B, adotamos try-with-resources. Para as contas duplicadas, preferimos a sequence do banco, aceitando a migração e reconhecendo que os dados antigos ainda precisam de auditoria.

## Rodrigo | madrugada, decisão final e lição

Nos comprovantes, corrigimos os novos registros e assumimos uma dívida: o histórico continuou invertido. Essa é uma decisão que reconsideraríamos, porque dinheiro correto não torna um comprovante errado aceitável.

Na factory, escolhemos uma exceção clara em vez de null. Isso melhora o diagnóstico, mas não entrega a conta universitária prometida. No Builder, campos obrigatórios continuam obrigatórios. E o teste de depósito só passa a provar algo quando confere o saldo esperado.

O pior momento é a TED: o contrato diz cinco reais e o código cobra cinquenta. Preferimos investigar e perder a janela a desativar o alarme. Na tabela do jogo, isso torna a suíte saudável. A abertura no horário é a proposta final desse cenário, mas reconheceríamos a necessidade de confirmar a versão corrigida antes de um Go real.

O placar simulado é reputação oito, prejuízo de noventa mil e dívida um: perfil A Noite dos Heróis. Reveríamos também o JDBC manual, reutilizando o repository para reduzir riscos de manutenção. A maior lição é que corrigir rápido exige preservar o contrato e saber qual evidência sustenta a decisão.

Nossa palavra é: responsabilidade.
