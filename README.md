# Raízes de integridade — publicadas fora da base de dados

Cada linha de `roots/*.jsonl` é a **raiz de um dia** de uma cadeia de auditoria, e cada raiz inclui
a do dia anterior. Alterar retroativamente um evento muda a raiz desse dia **e a de todos os dias
seguintes** — e essas já estão aqui, com a data em que foram publicadas.

## Algoritmo

```
sha256(prev_root || concat(event hashes of the day, sorted ascending))
```

Os hashes do dia são ordenados **pelo próprio hash**, não por id nem por data: id e data dependem do
sistema que os escreveu, o hash não. O formato canónico do evento e o verificador de referência
estão em `prompts/handoff/hash-chain-contract.md` do repositório do produto.

## Que cadeias estão aqui, e quais não estão

Ficheiro | cadeia | publicada desde
---|---|---
`roots/dflorahubai-audit.jsonl` | cadeia de auditoria do FloraCore (dflorahubai) | 2026-09-05

**Não está aqui a cadeia de lote do cbiotec-d.** Isso é ausência, não descuido: ela existe e ainda
não publica raiz nenhuma fora da sua base. Quem lê de fora não tem como distinguir as duas coisas, e
por isso esta secção existe — uma lista que não diz o que falta convida o leitor a assumir que está
tudo cá.

Consequência prática para quem escreve páginas: **«a raiz do dia» não identifica nada enquanto
houver mais do que uma cadeia.** Uma página que afirme publicação externa tem de dizer de que cadeia
fala, e verificar nesta tabela se essa cadeia está mesmo aqui.

## Como se usa isto

Quem tiver acesso aos dados recomputa a raiz de um dia e compara com a linha publicada. Se não
bater, os dados desse dia mudaram depois de a raiz ter saído daqui.

Quem **não** tiver acesso aos dados pode na mesma verificar que esta série nunca foi reescrita: o
histórico de commits deste repositório é append-only e datado pelo GitHub.

## O que isto garante, e o que não garante

Garante contra quem tem a credencial da base de dados. Essa pessoa reescreve as linhas **e**
recalcula todos os hashes internos, e a cadeia interna continua a verificar perfeitamente — mas não
consegue reescrever um valor que já saiu.

**Não** garante contra conluio entre quem escreve os dados e quem publica a raiz. Para isso seria
preciso um terceiro que datasse independentemente. O limite está aqui escrito em vez de implícito.

Os commits **não são assinados criptograficamente**: não há chave de assinatura na máquina que
publica, e uma chave guardada lá seria detida por quem tomasse essa máquina — precisamente o
atacante contra o qual isto defende. O que carrega o peso são os carimbos de tempo do GitHub e o
facto de o ramo não poder ser reescrito.

Publicação automática, uma vez por dia. O dia corrente nunca é publicado: ainda pode receber
eventos, e uma raiz publicada tem de ser final.
