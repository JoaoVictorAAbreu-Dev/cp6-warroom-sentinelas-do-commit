# Entrega e critérios | Sentinelas do Commit

## Situação atual

Preparação completa do percurso proposto, com ficha oficial ainda em branco. Não é uma entrega final comprovada. Nota 10 depende da avaliação docente, da participação e dos registros reais.

| Critério | Valor | Material preparado | O que falta para cumprir |
|---|---:|---|---|
| Sete rodadas | 3,5 | Sete justificativas propostas com mecanismo e trade-off | Debate, voto real, transcrição ao README, commit de cada rodada |
| Relâmpagos vividos | Até 1,5 | B, C e D no percurso proposto, com dependências corretas | Registrar somente os efetivamente acionados |
| Pós-mortem | 5,0 | Linha do tempo, caminho, duas causas raízes, duas alternativas, ações, perfil e palavra final | Reescrever conforme percurso real e montar na terceira aula |
| Repositório público | Obrigatório | Nome definido e repositório preparado | Verificar visibilidade pública após publicação |
| Template oficial | Obrigatório | Ficha provisória com membros e placar inicial | Incorporar template do Teams e seu dossiê |
| Commits por rodada | Obrigatório | Histórico de preparação identificado como proposta | Criar commits reais `decisao:` ao realizar cada rodada |
| Link no Teams | Obrigatório | Nome do repositório definido | Enviar link na atividade, conforme orientação docente |
| Apresentação de 2 a 3 minutos | Exigida | Roteiro dividido entre integrantes | Ensaiar e apresentar no formato indicado no Teams |

## Protocolo para realizar a atividade

1. Incorporar o template oficial como README, preservando identificação, dossiê e regras de negócio. Manter a proposta em pasta própria.
2. Debater a evidência com os integrantes antes de fechar o voto. A proposta já foi produzida com acesso às consequências; ela serve como estudo, não como prova de decisão sem conhecimento prévio.
3. Registrar o título, a letra escolhida, duas ou três linhas com mecanismo e custo e o placar após a consequência.
4. Comitar somente aquela rodada. O relâmpago pode acompanhar a rodada seguinte. Não fabricar datas nem reescrever commits de preparação como se fossem votos passados.
5. Conferir o placar antes de iniciar cada aula e usar as condições das cartas para aplicar os relâmpagos.
6. Na terceira aula, congelar perfil e placar, completar o pós-mortem real e apresentar. Registrar o commit final exigido.

## Comandos para os registros reais

Após editar o README com a decisão que o grupo realmente tomou:

```bash
git add README.md
git commit -m "decisao: R1 - opcao C (preservar rejeicao por saldo com teste de comportamento)"
git push
```

A letra e o resumo acima são um exemplo; usar a escolha efetiva. Repetir individualmente de R2 a R7, sem antecipar registros.

Para o relatório final:

```bash
git add README.md
git commit -m "pos-mortem: relatorio de incidente da madrugada"
git push
```

## Conferência final

- Repositório público: se privado, o PDF prevê menos 2,0 pontos.
- Histórico real por rodada: tudo em um único commit final pode custar 1,5 ponto. Commits `proposta:` não comprovam rodadas realizadas.
- README contém o template/dossiê oficial, sete decisões reais e só os relâmpagos aplicáveis.
- Placar deriva do próprio caminho; não misturar consequências de alternativas.
- Pós-mortem explica mecanismos e custos em vez de culpar pessoas.
- Link do GitHub postado no Teams; conferir instrução sobre identificação dos integrantes.
- Eventuais faltas: produzir arquivo individual conforme regra docente; não criar uma ausência fictícia para contornar participação.

Adotar como planejamento 23h59 do mesmo dia, conforme PDF, e conferir os prazos atuais na atividade do Teams.
