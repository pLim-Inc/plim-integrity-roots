# Como verificar uma raiz, sem acreditar em nós

Este ficheiro existe porque uma verificação que só o publicador consegue fazer não é uma
verificação.

## Se tem acesso aos dados

Recompute a raiz de um dia e compare com a linha publicada em `roots/*.jsonl`:

```
root(dia) = sha256( root(dia_anterior) || concat(hash de cada evento do dia, ordenados pelo hash) )
```

Os hashes são ordenados **pelo próprio hash** — não por id, não por data. Id e data dependem do
sistema que os escreveu, e a data depende até do fuso horário da sessão que a leu; o hash não
depende de nada disso.

O primeiro dia da série usa a string vazia como raiz anterior.

Se a sua conta não bater com a linha publicada, os dados desse dia mudaram **depois** de a raiz ter
saído daqui.

## Se não tem acesso aos dados

Ainda consegue verificar uma coisa útil: que esta série nunca foi reescrita. O histórico de commits
deste repositório é append-only e datado pelo GitHub, e o ramo está protegido contra reescrita e
apagamento. Compare a linha de um dia com a que viu ontem — ou com a que outra pessoa tenha
guardado.

## Carimbo de tempo independente (`stamps/`)

Cada raiz publicada é também carimbada em calendários OpenTimestamps, que ancoram um hash na
blockchain do Bitcoin. Ninguém aqui controla esses calendários nem essa blockchain.

```
ots verify stamps/dflorahubai-audit-2026-09-04.root.ots
```

O ficheiro `.root` tem a raiz desse dia e o `.ots` é a prova. Se alterar um único carácter do
`.root`, o cliente responde **`File does not match original!`** — medido.

Um carimbo acabado de fazer diz **`Pending confirmation in Bitcoin blockchain`**: é uma promessa dos
calendários que se torna âncora algumas horas depois. A publicação seguinte completa-a. Dizer
«pendente» enquanto está pendente é a razão de existir este parágrafo — um carimbo que se
anunciasse como prova antes de o ser valeria menos do que não existir.

**Porque é que isto importa mais do que parece:** publicar a raiz noutro sítio defende contra quem
tem a credencial da base de dados. Não defende contra conluio entre quem escreve os dados e quem
publica a raiz. Para esse caso é preciso um terceiro que date de forma independente, e é isto. Um
carimbo prova que um valor **existiu** antes de um momento; não preserva o valor — isso é o que faz
um segundo destino. As duas coisas não se substituem.

## O que isto garante, e o que não garante

**Garante** contra quem tem a credencial da base de dados. Essa pessoa reescreve as linhas **e**
recalcula todos os hashes internos, e a cadeia interna continua a verificar perfeitamente — mas não
consegue reescrever um valor que já saiu daqui.

**Não garante** contra conluio entre quem escreve os dados e quem publica a raiz. Para isso seria
preciso um terceiro que datasse independentemente. O limite está escrito porque um limite implícito
é uma promessa por cumprir.

Os commits **não são assinados criptograficamente**. Não há chave de assinatura na máquina que
publica, e uma chave guardada lá pertenceria a quem tomasse essa máquina — precisamente o atacante
contra o qual isto defende. O que carrega o peso são os carimbos de tempo do GitHub e um ramo que
não pode ser reescrito.

## Quem publica

Uma credencial dedicada, com escrita **só neste repositório** e sem qualquer acesso à API do GitHub:
não consegue ler outros repositórios, nem alterar as protecções deste. A publicação corre uma vez
por dia e nunca publica o dia corrente, porque um dia que ainda pode receber eventos não tem raiz
final.
