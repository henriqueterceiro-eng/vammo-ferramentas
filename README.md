# Ferramentas Vammo

**No ar:** https://henriqueterceiro-eng.github.io/vammo-ferramentas/

Dois lugares, um arquivo só (`index.html`):

1. **Notas fiscais** — você joga o PDF do DANFE e o app lê sozinho as ferramentas, a
   quantidade e o valor unitário. O que ele leu vai para uma tela de conferência antes
   de entrar no catálogo.
2. **Catálogo** — tudo que a Vammo já comprou, com último preço, preço médio, menor preço
   e o histórico de cada compra (data, NF, fornecedor).
3. **Pedidos** — você monta o pedido da base, o custo sai sozinho com o preço que a Vammo
   já pagou, e o pedido segue até o pagamento: gera o documento para o fornecedor, guarda
   a NF e o boleto e mostra em que etapa cada pedido está.

## Como usar

Abra o `index.html` no navegador (duplo clique já funciona) ou pela URL do GitHub Pages.

- **Importar nota**: arraste um ou vários PDFs para a área pontilhada. Se alguma ferramenta
  vier com preço diferente do que já estava cadastrado, a tela avisa antes de você gravar. Cada nota aparece
  com um selo: *soma confere com a nota* quer dizer que a soma dos itens lidos bate com o
  "valor total dos produtos" impresso — ou seja, nada ficou de fora.
- **Conferir**: dá para corrigir nome, unidade, quantidade e valor antes de gravar. Em
  *Destino* você decide se a linha vira ferramenta nova, se junta a uma que já existe
  (o app sugere quando reconhece) ou se é pulada (frete, material que não é ferramenta).
- **Abrir um pedido**: aba *Pedidos* → **+ Novo pedido** → escolha o fornecedor e a base
  (os dois são listas, não texto livre).
- **Adicionar ferramenta**: digite o nome e **clique no resultado** — ele fica *escolhido*,
  sem entrar no pedido ainda. Aí você ajusta a quantidade (escrevendo ou nos botões − / +)
  e clica em **Adicionar ao pedido**. Depois de adicionado, quantidade e preço continuam
  editáveis na linha, e o ✕ remove.
- **Kit no pedido**: entra **recolhido em uma linha só** (🧰 nome · N ferramentas), para não
  poluir a lista. O ▸ abre e mostra cada ferramenta; a quantidade na linha do kit é o
  **número de kits** e multiplica tudo que está dentro. O ✕ tira o kit inteiro.
  Na **exportação sai tudo aberto** — PDF, WhatsApp e CSV listam ferramenta por ferramenta,
  com o kit identificado.

### Fornecedores e bases

**Fornecedor** é escolhido de uma lista — hoje só a **ANT Ferramentas** (razão social, CNPJ,
IE, endereço e telefone tirados das próprias notas). Assim o nome não varia de um pedido
para outro e o app consegue conferir o CNPJ da nota: se você anexar um DANFE de outro
emitente, ele avisa que a nota é de outro fornecedor. Para cadastrar mais fornecedores,
a lista fica em `FORNECEDORES_PADRAO`, no começo do bloco de pedidos.

**Base de entrega** também é lista, com as bases do cadastro de oficinas da Vammo
(`mechanics_r.location`): **SBC, Osasco, Mooca e São Mateus**, mais *Nova base / outro
destino*, que abre um campo livre — é o caso de comprar ferramental para uma base que
ainda vai abrir. Fornecedor e base viraram obrigatórios para o pedido sair do rascunho.
- **Datas**: clique no campo e o calendário abre. Dá para digitar também — e uma data com
  ano impossível (o campo antigo estragava o ano enquanto você digitava) é recusada.
- **Mandar para o fornecedor**: **PDF A4** (documento com logo, CNPJ e assinatura),
  **WhatsApp** (copia o pedido em texto) ou **CSV**. O arquivo sai nomeado com o pedido e a
  base — `PED-2026-001 - Sao Mateus`.
- **Duplicar**: o pedido de uma base vira o da próxima com as mesmas ferramentas.
- **Preço usado**: último preço pago (padrão), médio ou menor — ajustável no Catálogo.
  Ao sair do rascunho o preço **congela**: vira o valor negociado e não segue mais o catálogo.

## A esteira do pedido

`Rascunho → Solicitado ao fornecedor → Confirmado → Recebido → NF lançada → Boleto recebido
→ Ag. subir pagamento → Ag. pagamento Vammo → Pago`

Clique na etapa para mover o pedido; cada uma guarda a data em que entrou, então dá para ver
onde ele parou. A lista mostra quanto está em aberto, quanto está aguardando pagamento e
quais pedidos passaram da data de entrega combinada.

Duas etapas exigem o documento: **NF lançada** só com o DANFE anexado, **Boleto recebido** só
com o boleto anexado.

### NF e boleto

- **NF**: o app lê o PDF e compara em duas frentes:
  1. **com o pedido** — item que não veio, quantidade diferente, preço acima do combinado;
  2. **com o preço já cadastrado** — quanto a Vammo pagava nessa ferramenta antes desta nota.
  O aviso de preço aparece em três lugares: no toast ao anexar, no topo da conferência
  (com a variação em %) e na lista de pedidos, com link para o pedido. Até 1% conta como
  arredondamento; acima de 5% o aviso fica vermelho.
  A referência é o preço **anterior à nota** — compras da própria nota e de notas mais novas
  ficam de fora, senão o preço novo viraria a própria referência e a divergência sumiria.
  O botão *lançar no catálogo* manda a nota para a tela de conferência, alimentando o histórico.
- **Boleto**: o app lê valor e vencimento da linha digitável, conferindo os dígitos
  verificadores (sem isso a leitura pega dígitos vizinhos e erra a casa decimal).
- Os PDFs ficam guardados no navegador (IndexedDB) e podem ser abertos a qualquer momento.

## Catálogo já pronto (`catalogo_ant.json`)

As 10 notas da ANT Ferramentas já estão cadastradas: **60 ferramentas, 136 compras,
R$ 93.117,69** em notas de 28/05 a 03/09/2026. Para carregar: **⬆ Restaurar** → escolha
`catalogo_ant.json`.

O nome de cada ferramenta foi enxugado (sai o código interno do fornecedor, fica
"Chave combinada 10mm · Mayle") e o texto original da nota virou apelido — a busca
continua achando pelo código. Nada de duplicata: quando a mesma ferramenta apareceu com
marcas diferentes, virou **uma** ferramenta com as duas compras no histórico, e o nome
fica sem marca (ex.: "Chave combinada 10mm", comprada Gedore e Mayle).

O catálogo vem **embutido no app**: abrir o link já mostra as 60 ferramentas, sem precisar
carregar nada. Ele só entra em navegador zerado — nunca por cima do que você já cadastrou.
Se o catálogo estiver vazio, aparece o botão *carregar o catálogo da ANT* para trazer de volta.

⚠️ **Por decisão do dono do app, o catálogo está no repositório público**: qualquer pessoa
com o link (ou navegando o GitHub) consegue ler o que a Vammo paga por cada ferramenta e de
quais fornecedores. Para fechar isso sem perder a praticidade, o caminho é Cloudflare Pages
com Access (login @vammo.com), também no plano grátis.

O `catalogo_ant.json` continua na pasta como backup solto e segue fora do git.

## Kits (combos)

Conjunto que se compra junto. Vem um pronto: **Carrinho para mecânicos (completo)** —
carrinho de 2 gavetas + o ferramental do mecânico + a chave de impacto pneumática,
**36 itens, R$ 1.962,44** por carrinho montado.

- Na aba **Catálogo**, o card *Kits* mostra o custo e abre o editor: dá para **incluir e
  excluir** ferramentas e mudar a quantidade de cada uma.
- No **pedido**, digite parte do nome e o kit aparece marcado com 🧰 — um clique traz todas
  as ferramentas dele de uma vez, cada linha identificada como *do kit*. O campo *Qtd* vale
  como número de kits (2 = dois carrinhos completos), e pedir o mesmo kit de novo soma na
  linha que já existe em vez de duplicar.

A composição saiu da NF 660524, que trouxe 10 carrinhos com 1 de cada ferramenta por
carrinho: as 34 ferramentas dela + o carrinho + a pneumática. Duas variações de modelo
ficaram de fora porque vieram em quantidade menor que 1 por carrinho (marreta de 1,5kg e
allen abaulada 4mm) — se fizerem parte, é só incluir pelo editor.

### Busca entende o nome de oficina

A mesma ferramenta tem nome diferente na bancada e na nota fiscal. A busca já traduz:
**allen ↔ hexagonal**, **chave L ↔ biela**, **philips ↔ cruzada**, **bico ↔ telefone/meia
cana**, **extensor ↔ extensão**, **catraca ↔ roquete**, **pneumática ↔ impacto**. Vale nos
três lugares: catálogo, montagem de kit e busca de item do pedido.

## Onde os dados ficam

No navegador desta máquina (localStorage) — nada é enviado para lugar nenhum, e a leitura
do PDF acontece no seu computador.

**Consequência prática**: o catálogo não é compartilhado entre pessoas nem entre máquinas,
e limpar os dados do navegador apaga tudo. Use **⬇ Backup** com frequência; o `.json`
gerado restaura em qualquer máquina pelo **⬆ Restaurar**.

Se depois virar ferramenta de time (todo mundo vendo o mesmo catálogo), o caminho é o
mesmo da Torre: Cloudflare Pages + D1 + login @vammo.com.

## Publicar no GitHub Pages

Repositório **público** = grátis — e, como o catálogo está embutido no `index.html`,
os preços de compra ficam públicos junto (veja o aviso acima).

```bash
git init
git add index.html README.md .gitignore
git commit -m "Ferramentas Vammo: catalogo por NF + custo por base"
git branch -M main
git remote add origin git@github.com:<usuario>/<repo>.git
git push -u origin main
```

Depois: **Settings → Pages → Source: Deploy from a branch → main / (root)**. Em um ou dois
minutos o app está em `https://<usuario>.github.io/<repo>/`.

Precisa de internet na primeira carga: a biblioteca que lê PDF (pdf.js 3.11.174) vem do
cdnjs. O resto do app funciona offline.

## Como o app lê a nota

O texto de um PDF sai fora de ordem, então regex em texto corrido não resolve. A tabela de
produtos do DANFE é um grid: o app acha o cabeçalho, deduz onde começa e termina cada
coluna (inclusive olhando as faixas de x que nenhum texto ocupa, que são as divisórias
impressas) e fatia cada linha pela posição. Depois monta a descrição juntando as linhas de
continuação — sabendo que alguns emissores imprimem a descrição **acima** da linha dos
números, e outros **abaixo**.

Aferido contra **57 DANFEs reais**: em todos, a soma dos itens lidos bateu com o valor
total dos produtos impresso na nota, e nenhuma descrição saiu vazia ou truncada. Testado
também dentro do navegador (Chrome), da leitura do arquivo até o custo da base.

## O que ele não faz

- **Nota escaneada / foto** não é lida — o app precisa do PDF com camada de texto (o DANFE
  que o fornecedor manda por e-mail é assim). Nota digitalizada exigiria OCR.
- **Nota de serviço (NFS-e)** não tem tabela de produtos; o app avisa em vez de inventar itens.
- Quando a nota traz um total que não fecha com os próprios itens, o app mostra
  **⚠ soma ≠ nota** e deixa você decidir — não corrige por conta própria.
