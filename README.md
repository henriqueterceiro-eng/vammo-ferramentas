# Ferramentas Vammo

**No ar:** https://henriqueterceiro-eng.github.io/vammo-ferramentas/

Dois lugares, um arquivo só (`index.html`):

1. **Notas fiscais** — você joga o PDF do DANFE e o app lê sozinho as ferramentas, a
   quantidade e o valor unitário. O que ele leu vai para uma tela de conferência antes
   de entrar no catálogo.
2. **Catálogo** — tudo que a Vammo já comprou, com último preço, preço médio, menor preço
   e o histórico de cada compra (data, NF, fornecedor).
3. **Compra por base** — você monta a lista de ferramentas que a base precisa e o custo
   sai sozinho, com o preço que a Vammo já pagou.

## Como usar

Abra o `index.html` no navegador (duplo clique já funciona) ou pela URL do GitHub Pages.

- **Importar nota**: arraste um ou vários PDFs para a área pontilhada. Cada nota aparece
  com um selo: *soma confere com a nota* quer dizer que a soma dos itens lidos bate com o
  "valor total dos produtos" impresso — ou seja, nada ficou de fora.
- **Conferir**: dá para corrigir nome, unidade, quantidade e valor antes de gravar. Em
  *Destino* você decide se a linha vira ferramenta nova, se junta a uma que já existe
  (o app sugere quando reconhece) ou se é pulada (frete, material que não é ferramenta).
- **Montar a lista da base**: aba *Compra por base* → **+ Nova lista** → digite parte do
  nome da ferramenta, escolha, ajuste a quantidade. O total recalcula na hora.
- **Duplicar**: montou o kit de uma base? **Duplicar** cria a lista da próxima base com
  as mesmas ferramentas.
- **Preço usado no cálculo**: último preço pago (padrão), preço médio ou menor preço já pago.
- **Exportar**: CSV ou imprimir/PDF (a impressão já sai limpa, sem menu e sem botões).

## Catálogo já pronto (`catalogo_ant.json`)

As 6 notas da ANT Ferramentas já estão cadastradas: **49 ferramentas, 55 compras,
R$ 40.462,93** em notas de 28/05 a 03/09/2026. Para carregar: **⬆ Restaurar** → escolha
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
