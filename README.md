# Coleta e Preparacao de Dados: Web Scraping e Crawler

# Relatório de Desenvolvimento – Web Scraping com Beautiful Soup

## 1. Introdução
O código tem como objetivo implementar um processo simples de web scraping a partir de uma página da Wikipédia, coletando dados estruturados e organizando-os em arquivos no formato CSV. A atividade foi dividida em duas partes: a primeira responsável pela coleta dos HTMLs das páginas; e a segunda voltada para a extração de informações dos arquivos previamente baixados.

A página selecionada , como exemplo, foi a da Wikipédia sobre Thomas Bayes. O motivo foi devido a sua importância como matemático e por suas contribuições fundamentais à teoria das probabilidades, em especial ao teorema que leva seu nome. A escolha se deu por estar diretamente relacionada ao conteúdo estudado na área de Ciência de Dados, além de ser uma página com boa quantidade de conexões internas, favorecendo o processo de navegação e coleta de dados. Essa característica permitiu obter um conjunto mais rico de páginas e informações para análise.

O desenvolvimento das atividades permitiu aplicar conceitos fundamentais de coleta e preparação de dados, especialmente no que se refere à manipulação de HTML, uso de bibliotecas de requisição e estruturação de dados para análise posterior.

---

## 2. Lógica Utilizada

### Parte 1 – Coleta dos HTMLs
Primeiro, definiu-se uma URL de entrada correspondente à página da Wikipédia sobre Thomas Bayes. 

A partir dessa página, foi feita a requisição HTTP utilizando a biblioteca `requests`. Para evitar bloqueios por parte do servidor, foi necessário incluir um header do tipo `User-Agent`, simulando o comportamento de um navegador.

O HTML da página inicial foi então processado com a biblioteca `BeautifulSoup`. Esta permite transformar o conteúdo HTML em uma estrutura navegável. A partir de então, foram identificadas as tags mais importantes: p.ex. `<a>` com atributo `href`.

Para diminiur os número de links, utilizou-se filtro para selecionar apenas os links internos da Wikipédia, ou seja, aqueles cujo `href` começa com `/wiki/`. Posteriormente, tais links foram convertidos em URLs completas com o uso da função `urljoin`, permitindo o acesso direto às páginas.

Cada link encontrado foi utilizado para baixar o HTML correspondente, que foi salvo em arquivos dentro da pasta `html_pages`. Esse processo gerou um conjunto de páginas que serviriam de base para a segunda parte do trabalho.

---

### Parte 2 – Extração e Estruturação dos Dados
Na segunda parte, o programa percorre todos os arquivos HTML armazenados na pasta `html_pages`. Para cada arquivo, o conteúdo é aberto e novamente processado com `BeautifulSoup`.

A partir de cada página, foram extraídas as seguintes informações:
* título da página, obtido pela tag `<h1>`;
* primeiro parágrafo, obtido pela primeira ocorrência da tag `<p>`;
* links internos, filtrados novamente pelo padrão `/wiki/`;
* links de imagens, obtidos pela tag `<img>`.

No caso das imagens, foi necessário tratar os links para que se tornassem acessíveis diretamente no navegador. Muitos desses links aparecem como caminhos relativos (por exemplo, iniciando com `/static/`). Para resolver isso, foi utilizado o `urljoin`, transformando esses caminhos em URLs completas.

Os dados coletados foram organizados em duas estruturas:
1. Uma lista com algumas informações (título, primeiro paragrafo, links_internos, timestamp) por página;
2. Uma lista contendo os links de cada imagem do artigo.

Essas listas foram posteriormente convertidas em arquivos CSV utilizando a biblioteca `csv`, por meio da classe `DictWriter`.

---

## 3. Análise dos Resultados
Ao final da execução, foram gerados dois arquivos: 

O arquivo `dados_paginas.csv` contém, para cada página analisada:
* título;
* primeiro parágrafo;
* links internos;
* timestamp da coleta.

Esse conjunto de dados permite ter uma visão geral do conteúdo textual das páginas e das conexões entre elas por meio dos links internos.

Outro arquivo é o `links_imagens.csv` que contém:
* nome da página de origem;
* link da imagem;
* timestamp.

Diferentemente do primeiro arquivo, aqui cada linha representa uma imagem, e não uma página. Por isso, o número de linhas é significativamente maior. Isso reflete a quantidade de imagens presentes nas páginas analisadas.

Em relação aos resultados, a execução do crawler resultou em um número significativo de links internos e imagens. 

A quantidade de links internos encontrados na página inicial foi de 141 links, o que levou à geração de diversos arquivos HTML na etapa de coleta. 

Na segunda etapa, ao processar esses arquivos, foram identificados  de links de imagens, refletindo a grande quantidade de elementos visuais presentes nas páginas da Wikipédia. 

Os números demonstram que, mesmo com um código relativamente simples, é possível obter um volume considerável de dados a partir de uma única página inicial, evidenciando o potencial do web scraping para coleta de dados.

De forma geral, os arquivos gerados estão consistentes com o esperado, permitindo a exploração posterior dos dados coletados.

---

## 4. Dificuldades Encontradas

Durante o desenvolvimento do trabalho, foram encontradas algumas dificuldades relevantes.

Uma das primeiras dificuldades foi o bloqueio da requisição HTTP pela Wikipédia. Inicialmente, o acesso à página retornava erro, o que foi resolvido com a inclusão de um header `User-Agent`. Esse ajuste foi essencial para permitir a coleta dos dados.

Outra dificuldade importante foi relacionada aos links das imagens. Os valores retornados no atributo `src` nem sempre eram URLs completas, o que impedia o acesso direto pelo navegador. Esse problema foi resolvido utilizando a função `urljoin`, garantindo que todos os links fossem completos e utilizáveis.

Também houve dificuldade na manipulação de arquivos CSV. No início, houve incerteza sobre como estruturar os dados corretamente e como escrever múltiplas linhas no arquivo. O uso do `csv.DictWriter` facilitou esse processo, mas exigiu entendimento prévio da estrutura de dicionários e da definição dos campos (`fieldnames`).

Um problema mais sutil, porém significativo, ocorreu na execução do código no Jupyter Notebook. Ao dividir o código em várias células (Markdown + código), houve diferença nos resultados em comparação com a execução do código completo de forma sequencial. Isso aconteceu porque parte da lógica, especialmente o laço `for` responsável por percorrer os arquivos HTML, foi separada em células diferentes. Como consequência, apenas o último elemento era processado nas etapas seguintes. Após análise, foi identificado que o problema estava relacionado à indentação e à quebra do fluxo do laço entre células. A solução foi manter toda a lógica do laço dentro de uma única célula.

Outra dificuldade foi perceber que a pasta `html_pages` acumulava arquivos de execuções anteriores. Isso fazia com que os resultados variassem mesmo sem alteração no código. A solução foi limpar a pasta antes de cada execução.

---

## 5. Conclusão

O trabalho permitiu compreender na prática o processo de coleta e preparação de dados a partir de páginas web. Foram aplicados conceitos importantes como requisições HTTP, parsing de HTML, filtragem de elementos e organização de dados em formato tabular.

Além disso, a atividade evidenciou a importância de aspectos práticos como tratamento de erros, padronização de dados e controle do ambiente de execução.

Acredita-se que os resultados obtidos foram satisfatórios e coerentes com os objetivos propostos, permitindo a geração de dados estruturados a partir de conteúdo não estruturado, que é uma etapa fundamental em projetos de Ciência de Dados.
