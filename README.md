# Gerador de fichas — SISTRAN-SP

Ferramenta web de arquivo único para gerar, em formato `.docx`, as fichas do **Plano de Trabalho** e do **Relatório Anual** do Plano de Segurança Viária do Estado de São Paulo (PSV-SP), a partir de dados do plano incorporados ao próprio código.

## Descrição

O **Gerador de fichas** apoia a elaboração dos documentos de planejamento e de acompanhamento do PSV-SP no âmbito do SISTRAN-SP. A pessoa usuária escolhe o tipo de documento, o eixo temático e os itens desejados (produtos ou indicadores de objetivos estratégicos); a página monta o arquivo `.docx` com as fichas já preenchidas com os dados de identificação, deixando em branco os campos que dependem de preenchimento manual (status, riscos, articulações, etapas etc.).

A ferramenta é composta por **um único arquivo HTML** que contém interface, lógica, dados do plano e modelos de documento. Não há back-end: a geração ocorre inteiramente no navegador.

> **Observação:** o código não indica se a ferramenta está publicada, hospedada ou integrada a outros sistemas, nem registra validação institucional dos documentos produzidos. Este README não afirma nenhum desses pontos.

## Funcionalidades

- **Dois tipos de documento:** Plano de Trabalho e Relatório Anual, cada um com modelo `.docx` próprio.
- **Seleção de eixo temático:** 8 eixos; o nome do eixo é gravado no cabeçalho como "Câmara temática".
- **Dois conteúdos, gerados em arquivos separados:**
  - **Ações e produtos:** uma ficha por ação, seguida das fichas dos produtos selecionados.
  - **Objetivos estratégicos (OE):** uma ficha por indicador de OE.
- **Busca e filtro por órgão** na lista de produtos.
- **Seleção em bloco:** marcar uma ação seleciona seus produtos; marcar um OE seleciona seus indicadores.
- **Ciclo de metas definido pelo ano** do documento, com aviso quando o produto não tem meta no ciclo.
- **Validação do campo Mês/Ano** do rodapé.
- **Funcionamento sem instalação e sem servidor** (ver [Requisitos](#requisitos)).

### Dados incorporados (conforme o código)

| Elemento | Quantidade |
|---|---|
| Eixos temáticos | 8 |
| Objetivos estratégicos (OE) | 25 |
| Ações | 98 |
| Produtos | 308 |
| Indicadores de OE | 51 |
| Órgãos responsáveis distintos (nos produtos) | 11 |

Os eixos são: 1 Gestão da Segurança Viária · 2 Gestão da Informação · 3 Vias Seguras · 4 Educação · 5 Comunicação · 6 Fiscalização · 7 Veículos Seguros · 8 Atendimento às Vítimas.

## Como funciona

1. A página carrega o objeto de dados `D` (chaves `P`, `A`, `E`, `O`, `I`), os fragmentos de XML dos modelos `S` e os modelos `.docx` em base64 `T`, todos embutidos no próprio HTML.
2. A pessoa usuária escolhe documento, eixo e conteúdo, e seleciona produtos ou indicadores.
3. Ao clicar em **Gerar**, a função `gerarDocx` substitui marcadores do tipo `@@CAMPO@@` nos trechos de XML do modelo, grava `word/document.xml`, `word/header1.xml` e `word/footer1.xml` no modelo `.docx` e compacta o resultado com JSZip (compressão DEFLATE).
4. A página exibe um resumo, eventuais avisos e o botão **Baixar documento (.docx)**.

O código não utiliza `DOMParser` nem `XMLSerializer`; a edição é feita por substituição de texto nos trechos de XML.

### Estrutura dos dados (`D`)

| Chave | Conteúdo |
|---|---|
| `E` | Eixos: código → nome |
| `O` | Objetivos estratégicos: código → nome |
| `A` | Ações: código → nome, OE e eixo |
| `P` | Produtos: código, nome, órgão responsável (`r`), detalhamento (`d`), ator envolvido (`a`), ação, eixo e lista de indicadores com metas por ciclo (`i`) |
| `I` | Indicadores de OE por OE: código, nome, "o que mede", fórmula, fonte e periodicidade |

## Tecnologias utilizadas

- HTML, CSS e JavaScript (sem framework).
- [JSZip](https://stuk.github.io/jszip/) v3.10.2, **incorporada ao HTML** (o cabeçalho da biblioteca informa licença dual MIT ou GPLv3).
- Fonte [Open Sans](https://fonts.google.com/specimen/Open+Sans), carregada do **Google Fonts** (`fonts.googleapis.com` e `fonts.gstatic.com`). Se o carregamento falhar, a página usa Helvetica, Arial ou `sans-serif`.
- Formato [Office Open XML](https://learn.microsoft.com/office/open-xml/open-xml-sdk) (`.docx`) para a saída.

## Requisitos

- Navegador moderno com JavaScript habilitado.
- Conexão com a internet **opcional**: ela é usada apenas para a fonte Open Sans; a geração dos documentos não depende de rede, pois dados, modelos e JSZip estão embutidos.
- Não há dependências a instalar, processo de build nem servidor.
- Para abrir os `.docx` gerados, é necessário um editor compatível (por exemplo, Microsoft Word). A compatibilidade com cada versão ou editor **não foi verificada** neste README.

## Instalação e execução

### Execução local

1. Obtenha o arquivo HTML (clonando o repositório ou baixando-o):
   ```bash
   git clone <URL-DO-REPOSITORIO>
   cd <NOME-DO-REPOSITORIO>
   ```
2. Abra o arquivo `Gerador de fichas — SISTRAN-SP.html` no navegador (duplo clique ou *Arquivo › Abrir arquivo*).

Não é necessário servidor local. Caso o seu navegador ou ambiente bloqueie algum recurso ao abrir o arquivo diretamente, sirva a pasta com qualquer servidor estático (por exemplo, `python3 -m http.server`) e acesse `http://localhost:8000`. Essa ressalva é genérica; o código não exige servidor.

### Publicação na internet (opcional)

O código não define nenhum processo de publicação. Por se tratar de um arquivo estático, ele pode, em princípio, ser servido por qualquer hospedagem de arquivos estáticos (por exemplo, GitHub Pages), o que deve ser configurado e validado pela equipe responsável.

## Como utilizar

1. **Documento:** escolha **Plano de Trabalho** ou **Relatório Anual**.
2. **Eixo:** selecione o eixo. O nome dele preenche o campo somente leitura **Câmara temática** e vai para o cabeçalho do documento. Cada documento é de **um único eixo**; para outro eixo, gere outro documento.
3. **Conteúdo:** escolha **Ações e produtos** ou **Objetivos estratégicos**. Cada opção gera um arquivo separado, e as seleções de uma e de outra são independentes.
4. **Dados do documento:** confira o **Mês/Ano (rodapé)**. O campo vem preenchido com o mês e o ano atuais e exige o formato `Mês/Ano`, por exemplo `Outubro/2026`.
5. **Seleção:**
   - *Ações e produtos:* defina o **ano** (de 2025 a 2035; no Relatório Anual o rótulo é "Ano de referência do relatório"). Use o filtro **Órgão responsável** e a busca por código ou palavra (por exemplo, `2.1` ou `Infosiga`; a busca considera código e nome do produto e da ação). Marque produtos individuais ou uma ação inteira. Com filtro ativo, marcar uma ação seleciona apenas os produtos exibidos.
   - *Objetivos estratégicos:* marque o OE para incluir todos os indicadores dele ou escolha indicadores individuais. **Marcar todos os indicadores do eixo** e **Limpar seleção** também estão disponíveis. OEs sem indicadores aparecem desabilitados.
6. Clique em **Gerar fichas…** e, depois, em **Baixar documento (.docx)**.
7. Abra o arquivo no Word e preencha os campos em branco.

### Ciclo de metas

O ano selecionado define a coluna de metas usada nas fichas de produtos:

| Ano | Ciclo |
|---|---|
| até 2027 | 2025 – 2027 |
| 2028 a 2030 | 2028 – 2030 |
| 2031 em diante | 2031 – 2035 |

Somente os indicadores com meta no ciclo aparecem na ficha. Se **nenhum** indicador do produto tiver meta no ciclo, a página exibe um aviso (`Produto X: nenhum indicador com meta no ciclo …`) e a linha de indicador/meta fica em branco. Com os dados atuais, 117, 134 e 199 produtos não têm meta nos ciclos 2025–2027, 2028–2030 e 2031–2035, respectivamente.

## Arquivos gerados

- **Formato:** `.docx`, construído a partir do modelo do tipo de documento escolhido (o modelo inclui cabeçalho, rodapé, logotipo e fontes incorporadas).
- **Nomes dos arquivos:**
  - Ações e produtos: `Plano_de_Trabalho_Eixo<N>_Acoes_Produtos_<ano>.docx` ou `Relatorio_Anual_Eixo<N>_Acoes_Produtos_<ano>.docx`
  - Objetivos estratégicos: `Plano_de_Trabalho_Eixo<N>_OEs.docx` ou `Relatorio_Anual_Eixo<N>_OEs.docx`
- **Organização (ações e produtos):** fichas de ação em ordem de código, cada uma seguida das fichas de seus produtos, em ordem de código.
- **Organização (OE):** uma ficha por indicador, em ordem de código, com quebra entre fichas.
- **Cabeçalho:** "Câmara temática: *nome do eixo*". **Rodapé:** mês/ano informado, mais endereço e contato fixos do modelo (o Plano de Trabalho traz um e-mail; o Relatório Anual, um telefone).
- **Preenchimento automático:** código, nome, responsável, detalhamento e ator envolvido do produto; indicadores e metas do ciclo; nome da ação; dados do indicador de OE (o que mede, fórmula, fonte, periodicidade).
- **Preenchimento manual:** todos os demais campos permanecem como orientações do modelo, a serem completados no Word. No Plano de Trabalho: problema enfrentado, propósito, relação com municípios, articulações, riscos, status atual, etapas previstas, relação com projetos existentes, valor atual, processo de coleta, entre outros. No Relatório Anual: articulações executadas, riscos, avanços realizados, status de implementação, adaptação de programas existentes, desafios identificados, valor alcançado, entre outros.

## Estrutura do repositório

Estrutura **sugerida** (o código-fonte analisado é um único arquivo; os demais itens são opcionais):

```text
.
├── Gerador de fichas — SISTRAN-SP.html   # aplicação completa (interface, dados, modelos e JSZip)
├── README.md
└── LICENSE                               # a definir
```

## Limitações e observações

- **Um eixo por documento.** O código interrompe a geração com erro se a seleção abranger mais de um eixo. Na interface, trocar de eixo reconstrói as listas e descarta as marcações anteriores.
- **Dados incorporados.** Dados do plano, textos e metas estão fixos no HTML; qualquer atualização exige editar o objeto `D` no arquivo. O código não indica a fonte desses dados nem como foram extraídos; isso precisa ser confirmado pela equipe responsável.
- **Modelos incorporados.** Os modelos `.docx` (`T`) e os trechos de XML (`S`) estão em base64/JSON no HTML. Um comentário no código informa que os trechos foram pré-montados por um script Python (`esqueleto.py`), que **não faz parte do HTML analisado**; para alterar os modelos, é preciso localizá-lo ou refazer o procedimento.
- **Texto institucional fixo.** Endereço, e-mail e telefone do rodapé fazem parte dos modelos.
- **Opções internas não expostas na interface.** `gerarDocx` aceita parâmetros (`incluirOE`, `todosOE`, `saida`) que a interface atual não utiliza.
- **Download.** O botão tenta usar o recurso `claude.use('downloads')` quando existente no ambiente de execução; caso contrário, usa o download padrão do navegador.
- **Tamanho.** O arquivo HTML tem cerca de 7,5 MB, em razão de dados, modelos e fontes incorporados.
- **Testes e validação.** Não foram identificados testes automatizados no código, e não há registro de validação institucional dos documentos gerados.
- Os documentos gerados são rascunhos estruturados: o conteúdo remanescente deve ser preenchido e revisado manualmente.

## Licença e autoria

- **Licença do projeto:** não identificada no código; **a definir**.
- **Autoria e responsável institucional:** não identificados no código; **a definir**.
- **Terceiros:** JSZip v3.10.2 (licença dual MIT ou GPLv3, conforme cabeçalho da biblioteca); Open Sans, via Google Fonts.
