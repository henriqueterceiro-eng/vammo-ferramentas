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

- **Importar nota**: arraste um ou vários PDFs para a área pontilhada. Cada nota aparece
  com um selo: *soma confere com a nota* quer dizer que a soma dos itens lidos bate com o
  "valor total dos produtos" impresso — ou seja, nada ficou de fora.
- **Conferir**: dá para corrigir nome, unidade, quantidade e valor antes de gravar. Em
  *Destino* você decide se a linha vira ferramenta nova, se junta a uma que já existe
  (o app sugere quando reconhece) ou se é pulada (frete, material que não é ferramenta).
- **Abrir um pedido**: aba *Pedidos* → **+ Novo pedido** → preencha fornecedor e base,
  busque as ferramentas e ajuste quantidade e preço. O total recalcula na hora.
- **Mandar para o fornecedor**: **PDF A4** (documento com logo, CNPJ e assinatura),
  **WhatsApp** (copia o pedido em texto) ou **CSV**.
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

- **NF**: o app lê o PDF e **compara com o pedido** — item que não veio, quantidade diferente,
  preço acima do combinado. E o botão *lançar no catálogo* manda a nota para a tela de
  conferência, alimentando o histórico de preços.
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

Esse arquivo **não deve ir para o repositório público** — são os preços de compra da
Vammo. Deixe ele fora do git (já está no `.gitignore`) e carregue pelo Restaurar.

## Onde os dados ficam

No navegador desta máquina (localStorage) — nada é enviado para lugar nenhum, e a leitura
do PDF acontece no seu computador.

**Consequência prática**: o catálogo não é compartilhado entre pessoas nem entre máquinas,
e limpar os dados do navegador apaga tudo. Use **⬇ Backup** com frequência; o `.json`
gerado restaura em qualquer máquina pelo **⬆ Restaurar**.

Se depois virar ferramenta de time (todo mundo vendo o mesmo catálogo), o caminho é o
mesmo da Torre: Cloudflare Pages + D1 + login @vammo.com.

## Publicar no GitHub Pages

Repositório **público** = grátis. Como o app sobe vazio (os dados ficam no seu
navegador), nenhum preço da Vammo vai para a internet.

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
