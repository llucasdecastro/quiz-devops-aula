# Respostas da aula guiada

Equipe: individual (sem dupla)

Integrantes: Lucas de Castro Paranhos Tourinho

Repositório: https://github.com/llucasdecastro/quiz-devops-aula

Endereço publicado: https://llucasdecastro.github.io/quiz-devops-aula/

Q01 a Q22 respondidas pelo próprio aluno (trabalho individual). Q23 e Q24 são individuais; as respostas abaixo são do integrante identificado em cada uma.

## Histórico real de commits (branch main)

| Etapa | Commit | Mensagem |
|---|---|---|
| 2 | c801803 | Cria primeira versao do quiz |
| 3 | 79d0aed | Personaliza titulo do quiz |
| 5 | 6c22191 | Adiciona testes da pontuacao |
| 5 | 06a0a20 | Configura comando de testes |
| 6 | 44e72c4 | Configura CI do quiz |
| 8 | 48cd762 | Demonstra falha na pontuacao |
| 8 | 7c21845 | Corrige pontuacao |
| 9 | 598323b | Adiciona publicacao automatica |
| 10 | 98eb57a | Atualiza titulo publicado |
| 11 | 7a9a5d2 | Impede publicacao com erro |
| 11 | 1afb407 | Recupera fluxo de publicacao |
| 12 | 5bd159b | Amplia verificacao de respostas em branco |

## Execuções no Actions

Base: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/

| Nº | ID | Evento | Commit | Resultado |
|---|---|---|---|---|
| 1 | 45191947537 | push | 44e72c4 | sucesso, 5 testes aprovados |
| 2 | 45192152459 | workflow_dispatch | 44e72c4 | sucesso |
| 3 | 45194065643 | push | 48cd762 | falha, 3 aprovados e 2 reprovados |
| 4 | 45194193404 | push | 7c21845 | sucesso |
| 5 | 45194572954 | push | 598323b | sucesso, testes e publicação |
| 6 | 45194767823 | push | 98eb57a | sucesso, título versao 2 publicado |
| 7 | 45194912128 | push | 7a9a5d2 | testes reprovados, Publicar site skipped |
| 8 | 45195128336 | push | 1afb407 | testes OK, publicação falhou (ver nota na Q22) |
| 9 | 45195483550 | workflow_dispatch | 1afb407 | testes OK, publicação falhou (mesma causa do nº 8) |
| 10 | 45195724979 | push | 5bd159b | sucesso, 6 testes aprovados e publicação concluída |

## Etapa 01 Preparação do projeto e das contas

### Q01

Se queremos automatizar os testes e disponibilizar o site por um endereço, qual responsabilidade cabe ao GitHub Actions e qual cabe ao GitHub Pages?

Resposta

O GitHub Actions executa a automação: a cada alteração ele cria um ambiente (runner), busca o código, instala o Node.js e roda os testes. O GitHub Pages hospeda os arquivos estáticos e os entrega por um endereço público (https). Um verifica, o outro publica.

Observação após a execução

No repositório, o Actions rodou os testes em cada commit na main (aba Actions) e, no workflow final, o job de publicação enviou a pasta site ao Pages, que passou a servir o quiz em https://llucasdecastro.github.io/quiz-devops-aula/.

### Q02

Que problema operacional pode ocorrer se cada integrante publicar uma cópia diferente do código sem registrar a versão utilizada?

Resposta

Ninguém saberia qual versão está no ar nem qual código foi testado. Isso gera conflitos entre cópias, perda de alterações, dificuldade para reproduzir um erro e impossibilidade de voltar a uma versão anterior que funcionava.

Observação após a execução

Com um único repositório e a branch main, cada mudança virou um commit identificado, o que permitiu relacionar cada execução do Actions a uma versão exata do código.

Evidência

Repositório: https://github.com/llucasdecastro/quiz-devops-aula. Branch: main.

## Etapa 02 Código inicial e primeiro commit

### Q03

Que informação o commit registra para que outra pessoa consiga localizar esta entrega depois?

Resposta

O commit registra um identificador único (hash), o autor, a data e hora, a mensagem descritiva e o conjunto exato de alterações em relação ao commit anterior. Com isso é possível localizar a entrega e ver o que mudou.

Observação após a execução

O primeiro commit ficou com o identificador curto c801803 e a mensagem Cria primeira versao do quiz.

### Q04

O que pode acontecer com nosso pipeline se site/index.html ficar dentro de projeto_inicial/site/index.html no repositório?

Resposta

Os caminhos esperados deixam de existir. O teste importa ../site/pontuacao.js e o deploy empacota ./site, então ambos procurariam uma pasta que não está na raiz. O teste falharia ao importar o módulo e a publicação não encontraria os arquivos do site.

Observação após a execução

Na conferência da raiz apareceram README.md, package.json e a pasta site, com index.html, style.css, app.js e pontuacao.js dentro dela, como o pipeline espera.

Evidência

Commit c801803, mensagem Cria primeira versao do quiz.

## Etapa 03 Mudança identificável e histórico

### Q05

Se uma execução futura apresentar um erro, por que devemos anotar o identificador do commit associado a ela?

Resposta

Porque o identificador aponta para a versão exata do código que rodou. Com ele dá para comparar com o commit anterior, achar a alteração que causou o erro e reproduzi-lo ou revertê-lo.

Observação após a execução

A execução nº 3 falhou e estava ligada ao commit 48cd762. Comparando com 7c21845, a única diferença era pontos += 2 contra pontos += 1.

### Q06

A mudança do título já iniciou algum teste automático neste momento? O que ainda precisamos configurar?

Resposta

Não. Ainda não existe workflow nem testes no repositório, então nada é disparado por um push. Precisamos criar os testes, o comando npm test no package.json e o arquivo de workflow em .github/workflows.

Observação após a execução

Consistente com o histórico: a primeira execução registrada no Actions (nº 1) é a do commit 44e72c4, que criou o workflow. Não existe execução associada ao commit do título 79d0aed.

Evidência

Commit 79d0aed. Diferença entre as versões: o h1 passou de Quiz Cloud e DevOps para Quiz Cloud e DevOps da equipe.

## Etapa 04 Regra de negócio que será protegida

### Q07

Por que executar testes sobre a mesma função utilizada pela interface ajuda a verificar a versão que vamos publicar?

Resposta

Porque o teste valida o próprio código que será publicado. O app.js importa calcularPontuacao de pontuacao.js e os testes importam o mesmo arquivo. Se a regra quebrar, o teste falha antes de a interface ser atualizada. Um teste sobre uma cópia da função poderia passar enquanto a versão publicada estivesse errada.

Observação após a execução

No log, os testes executaram tests/pontuacao.test.js importando ../site/pontuacao.js, o mesmo arquivo carregado pelo app.js no site.

### Q08

Defina o resultado esperado para três acertos e para nenhuma resposta marcada. Qual regra sustenta sua previsão?

Resposta

Três acertos resultam em 3 pontos. Nenhuma resposta marcada resulta em 0 ponto. A regra: a função percorre o gabarito, soma 1 ponto somente quando a resposta existe (não é null nem undefined) e é igual à correta. Resposta em branco nunca pontua.

Observação após a execução

Confirmado nos testes (3 respostas corretas somam 3; null e ausentes somam 0) e no site publicado: tudo certo mostrou 3 de 3, nenhuma resposta mostrou 0 de 3.

Evidência

Resultados previstos: 3 pontos para três acertos e 0 para nenhuma resposta. Caminho da função: site/pontuacao.js.

## Etapa 05 Testes automatizados e comando do projeto

### Q09

Qual é a sequência entre npm test, package.json e os arquivos de teste?

Resposta

O comando npm test lê o campo scripts.test do package.json, que contém node --test. Esse comando faz o Node.js localizar e executar os arquivos de teste (como tests/pontuacao.test.js) e terminar com código de saída 0 se todos passarem, ou diferente de 0 se algum falhar.

Observação após a execução

No log: "> quiz-devops@1.0.0 test" seguido de "> node --test", e depois a lista dos testes executados.

### Q10

Se o runner executar zero testes e terminar sem erro, isso é evidência suficiente para liberar a versão? O que você conferiria?

Resposta

Não. Zero testes executados sem erro não prova nada, porque nada foi verificado. Eu conferiria no log o total de testes (tests), quantos passaram (pass), quantos falharam (fail) e se os nomes dos casos esperados aparecem. Também conferiria o caminho do arquivo de teste e o script test do package.json.

Observação após a execução

Os logs mostraram "tests 5, pass 5, fail 0" nas primeiras execuções e "tests 6, pass 6, fail 0" na final, com o nome de cada caso.

Evidência

Arquivo de teste: tests/pontuacao.test.js. Arquivo de configuração: package.json, na raiz. Comando definido: "test": "node --test".

## Etapa 06 Primeiro workflow de integração contínua

### Q11

Preveja se o commit que cria esse YAML na main inicia o pipeline. Qual evento e qual filtro do arquivo justificam sua resposta?

Resposta

Sim, inicia. O evento é push e o filtro branches: [main] casa com o commit feito direto na main.

Observação após a execução

Confirmado: o commit 44e72c4 disparou a execução nº 1 automaticamente, com evento push.

### Q12

Qual é a vantagem de registrar o workflow no mesmo histórico do código da aplicação?

Resposta

O pipeline passa a ser versionado junto com o código: cada commit mostra qual regra de automação valia naquele momento, as mudanças no pipeline são revisáveis e reversíveis, e qualquer pessoa que clona o repositório recebe também a forma de testar e publicar.

Observação após a execução

O histórico passou a mostrar na mesma linha do tempo as mudanças de código e as do workflow (commits 44e72c4 e 598323b).

Evidência

Caminho do YAML: .github/workflows/ci-cd.yml. Commit que o criou: 44e72c4.

## Etapa 07 Execução no Actions e leitura dos logs

### Q13

Por que configuramos a versão do Node.js no runner? No trecho, o que significam v7 e 24?

Resposta

Para que o ambiente seja previsível e os testes rodem sempre com a mesma versão, em vez de depender do que o runner trouxer instalado. @v7 é a versão da action actions/setup-node que prepara o Node.js. 24 (node-version) é a versão do Node.js que será instalada.

Observação após a execução

No passo Preparar Node.js do log: "Run actions/setup-node@v7" e "Found in cache @ /opt/hostedtoolcache/node/24.22.3/x64".

### Q14

Que dados do log você registraria para comprovar que esta revisão foi verificada? Indique também a diferença entre uses e run.

Resposta

Registraria o commit (identificador e mensagem), a branch, o evento (push ou workflow_dispatch), o resultado do job, a versão do Node.js e o resumo dos testes (total, aprovados e falhas). A diferença: uses chama uma action pronta e reutilizável (como checkout e setup-node); run executa um comando de shell escrito por nós (como npm test).

Observação após a execução

A execução nº 1 mostrou commit 44e72c4, branch main, evento push, job com sucesso em 5s e resumo "tests 5, pass 5, fail 0".

Evidência

Execução automática (push): https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45191947537. Execução manual (workflow_dispatch): https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45192152459. Resumo real dos testes: 5 testes, 5 aprovados, 0 falhas, 0 cancelados, 0 pulados.

Trecho do log (execução nº 1, passo Executar testes):

```
> quiz-devops@1.0.0 test
> node --test
✔ nenhuma resposta correta soma zero (0.727121ms)
✔ uma resposta correta soma um ponto (0.116293ms)
✔ tres respostas corretas somam tres pontos (0.091315ms)
✔ respostas null nao somam pontos (0.701142ms)
✔ respostas ausentes nao somam pontos (0.129853ms)
ℹ tests 5
ℹ pass 5
ℹ fail 0
```

## Etapa 08 Falha controlada e correção

### Q15

Antes de executar, quais dos cinco casos você prevê que falharão ao dobrar o ponto por acerto? Justifique.

Resposta

Falham dois: "uma resposta correta soma um ponto" (daria 2 em vez de 1) e "tres respostas corretas somam tres pontos" (daria 6 em vez de 3). Os outros três continuam passando porque esperam 0 e 0 vezes 2 continua sendo 0.

Observação após a execução

Previsão confirmada exatamente: 3 aprovados, 2 reprovados. No caso 2 o erro foi 2 !== 1 e no caso 3 foi 6 !== 3.

### Q16

Por que reexecutar a execução defeituosa não substitui fazer o commit da correção? Use os identificadores das execuções na explicação.

Resposta

Porque reexecutar usa o mesmo commit e o mesmo código. A execução nº 3 (id 45194065643) rodou o commit 48cd762, com pontos += 2, e falharia de novo. Só o commit de correção 7c21845 muda o código e gera uma nova execução, a nº 4 (id 45194193404), que passa.

Observação após a execução

Reforçado na prática: na execução nº 8 (commit 1afb407) a reexecução do job de publicação gerou um segundo artefato github-pages e continuou falhando. Reexecutar não muda o código nem cria uma execução nova.

Evidência

Execução com erro: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45194065643. Execução corrigida: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45194193404. Esperado e recebido de testes reprovados: caso 2, esperado 1 e recebido 2; caso 3, esperado 3 e recebido 6.

Trecho do log (execução nº 3, commit 48cd762, passo Executar testes):

```
✖ uma resposta correta soma um ponto (0.551002ms)
✖ tres respostas corretas somam tres pontos (0.136336ms)
ℹ tests 5
ℹ pass 3
ℹ fail 2
  AssertionError [ERR_ASSERTION]: Expected values to be strictly equal:
  2 !== 1   (actual: 2, expected: 1)
  6 !== 3   (actual: 6, expected: 3)
Error: Process completed with exit code 1.
```

## Etapa 09 Publicação condicionada e configuração do Pages

### Q17

O que needs: testes exige antes de permitir o job publicar neste workflow? Por que o nome usado é testes?

Resposta

Exige que o job testes tenha terminado com sucesso. Se ele falhar ou for cancelado, publicar não executa. O nome usado é testes porque needs referencia o identificador (a chave) do job em jobs, e não o rótulo exibido em name (Testar pontuacao).

Observação após a execução

Na execução nº 5 os dois jobs apareceram encadeados, com testes antes de publicar. Na nº 7, com os testes reprovados, publicar ficou skipped.

### Q18

Como a publicação automática deste exercício caracteriza implantação contínua? Como seria uma liberação com decisão humana após os testes?

Resposta

Cada alteração aprovada nos testes segue para o endereço publicado sem intervenção humana, o que caracteriza implantação contínua (continuous deployment). Com decisão humana, o deploy ficaria aguardando uma aprovação manual (por exemplo, um ambiente protegido com revisor obrigatório ou um disparo manual) depois dos testes. Isso seria entrega contínua (continuous delivery).

Observação após a execução

O commit 98eb57a, que só alterou o título, foi testado e publicado sem nenhuma ação manual nossa.

Evidência

Commit que alterou o workflow: 598323b (Adiciona publicacao automatica). Linha que conecta os jobs: needs: testes

## Etapa 10 Artefato publicado e mudança visível

### Q19

O que o path ./site seleciona para o artefato de publicação? Por que precisamos conferir esse caminho?

Resposta

Seleciona somente a pasta site da raiz do repositório (index.html, style.css, app.js e pontuacao.js), e é isso que vira o artefato enviado ao Pages. Precisamos conferir porque, se o caminho estiver errado, o site publicado fica vazio, incompleto ou com a raiz errada, e o README, o package.json e os testes não deveriam ir para o site.

Observação após a execução

O site publicado carregou normalmente com os arquivos da pasta site. O artefato github-pages tinha cerca de 2 KB.

### Q20

Que evidências ligam a alteração do título à versão que está no endereço publicado?

Resposta

O commit 98eb57a (Atualiza titulo publicado), a execução nº 6 disparada por esse commit com testes e publicação concluídos, e o título visível no endereço público. O commit identifica a mudança, a execução prova que foi testada e publicada, e o endereço mostra o resultado.

Observação após a execução

Após o deploy, o h1 do site passou a exibir Quiz Cloud e DevOps versao 2.

Evidência

Endereço do site: https://llucasdecastro.github.io/quiz-devops-aula/. Commit do novo título: 98eb57a. Execução que publicou: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45194767823.

## Etapa 11 Bloqueio de uma versão com defeito

### Q21

A execução com falha retirou o site do ar? Explique o que você observou no endereço publicado e no job publicar.

Resposta

Não. Quando os testes falham, o job publicar não executa (skipped), então o Pages continua servindo a última versão aprovada.

Observação após a execução

Na execução nº 7 (commit 7a9a5d2), Testar pontuacao falhou e Publicar site ficou skipped. No endereço publicado o título continuava versao 2 e responder uma pergunta corretamente mostrou "Voce acertou 1 de 3 perguntas.". O pontuacao.js publicado seguia com pontos += 1, enquanto o repositório já tinha += 2. No log da execução nº 7 o resumo foi 5 testes, 3 aprovados e 2 reprovados: o caso 2 falhou com 2 !== 1 (esperado 1, recebido 2) e o caso 3 com 6 !== 3 (esperado 3, recebido 6), o mesmo resultado da execução nº 3. A página publicada, porém, ainda mostrava 1 ponto para um acerto, porque o código novo com defeito nunca chegou ao Pages.

### Q22

Como a mesma evidência demonstra integração contínua e controle da implantação? Identifique cada parte do processo.

Resposta

Integração contínua: a cada commit na main, o job Testar pontuacao integra a mudança ao código existente e roda os testes automaticamente. Controle da implantação: o needs: testes faz o job Publicar site só rodar após testes aprovados. Na execução nº 7 vemos as duas coisas juntas, testes reprovados e publicar skipped.

Observação após a execução

Nota honesta sobre a recuperação: o commit de correção 1afb407 passou nos testes, mas nas execuções nº 8 e nº 9 o job de publicação falhou com "No artifacts named github-pages were found", mesmo com o artefato gerado. Não havia incidente reportado no githubstatus.com no dia. Reexecutar dentro da execução nº 8 gerou um segundo artefato e o erro mudou para "Multiple artifacts". A recuperação completa, com testes e publicação aprovados, veio na execução nº 10 (commit 5bd159b), que já inclui a correção.

Evidência

Execução com testes reprovados e publicar skipped: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45194912128. Execução de recuperação com ambos aprovados: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45195724979 (as nº 8 e nº 9 falharam apenas na publicação, por causa transitória).

## Etapa 12 Novo teste e explicação individual do pipeline

Previsão de respostas [0, null, 2] contra gabarito [0, 1, 2]: 2 pontos (acerta a posição 0 e a 2, e null não pontua). Confirmada no teste respostas parcialmente em branco somam dois pontos.

### Q23

Integrante: Lucas de Castro Paranhos Tourinho

Se alguém remover needs: testes do job publicar, a falha de testes ainda garante o bloqueio? Qual configuração você restauraria e como comprovaria a correção?

Resposta

Não. Se remover `needs: testes` do job `publicar`, os dois jobs serão executados independentemente, permitindo que o site seja publicado mesmo que os testes falhem. Para corrigir, eu restauraria a configuração `needs: testes` dentro do job `publicar`. Comprovaria a correção fazendo um commit com um erro proposital no código e verificando se o job Publicar site aparece como skipped, impedindo a publicação.

Observação após a execução

Essa prova já foi feita na prática com o workflow completo: no commit 7a9a5d2 (execução nº 7), com pontos += 2, o job Testar pontuacao falhou e o job Publicar site ficou skipped.

### Q24

Integrante: Lucas de Castro Paranhos Tourinho

Escolha um comportamento de uma aplicação que seu grupo poderia construir. Proponha um teste com entrada e resultado esperado e explique em que momento do pipeline ele deve executar.

Resposta

Escolheria como exemplo um carrinho de compras. Criaria um teste para verificar o cálculo de desconto: se o cliente realizar uma compra de R$ 200 e utilizar um cupom de 10% de desconto, o valor final esperado deve ser R$ 180. Esse teste deve executar no job de testes, a cada push na branch `main`, antes da publicação do site. Assim, se o cálculo estiver incorreto, o pipeline bloqueia a publicação.

Observação após a execução

O mesmo vale para o nosso quiz: o sexto teste (respostas parcialmente em branco) rodou no job Testar pontuacao, antes de Publicar site, na execução nº 10 (commit 5bd159b).

Evidência

Link da execução final com seis testes: https://github.com/llucasdecastro/quiz-devops-aula/actions/runs/45195724979 (commit 5bd159b, 6 testes aprovados, publicação concluída).

Trecho do log (execução nº 10, commit 5bd159b):

```
Run actions/setup-node@v7
Found in cache @ /opt/hostedtoolcache/node/24.22.3/x64
> node --test
✔ nenhuma resposta correta soma zero (0.787465ms)
✔ uma resposta correta soma um ponto (0.130532ms)
✔ tres respostas corretas somam tres pontos (0.123007ms)
✔ respostas null nao somam pontos (0.830473ms)
✔ respostas ausentes nao somam pontos (0.143599ms)
✔ respostas parcialmente em branco somam dois pontos (0.106055ms)
ℹ tests 6
ℹ pass 6
ℹ fail 0
```
