# Porsche Sales Intelligence Dashboard

Dashboard interativa em **HTML único**, construída a partir da planilha sanitizada do desafio. A versão atual é local porque a conexão GitHub não foi habilitada nesta sessão.

## Como abrir

Abra [`index.html`](./index.html) no navegador. Não há build nem servidor obrigatório: os dados tratados ficam embutidos no próprio HTML e os gráficos são renderizados com HTML, CSS e JavaScript nativos.

## Perguntas de negócio escolhidas

1. **Qual modelo concentra a receita?**  
   Ajuda a priorizar estoque, campanhas e atenção comercial. O gráfico ordena os modelos por receita, não apenas por quantidade, porque uma venda de maior valor pode mudar a decisão.
2. **Em que ano a operação vendeu mais?**  
   Mostra a cadência comercial ao longo do tempo. Só entram datas válidas; os 24 registros com `INVALID` na coluna de data são preservados na base, mas não são inventados em uma série temporal.
3. **Quais meios de pagamento exigem mais atenção operacional?**  
   Cruza o método de pagamento com o status de entrega. Isso ajuda a revelar concentrações de pendências por meio, em vez de olhar pagamento e entrega isoladamente.

## Filtros

Os seis filtros recortam simultaneamente todos os indicadores e gráficos: **modelo, cidade, ano do veículo, método de pagamento, estado e status de entrega**. O topo recalcula vendas, receita, ticket médio e entregas concluídas.

## Tratamento da base antes da IA

- Usei apenas as colunas sanitizadas: modelo, ano do veículo, preço, quilometragem, método de pagamento, cidade, estado, status e data sanitizados.
- Removi do produto final `customer_name`, `salesperson` e todas as colunas cruas, evitando embutir nomes ou informações desnecessárias no HTML público.
- Converti `SalesPriceSanitized`, `VehicleMileageSanitized` e `ModelYearSanitized` para número.
- Converti datas reconhecíveis para `YYYY-MM-DD`; as 24 datas `INVALID` ficaram como vazias e não entram na série temporal.
- Criei `sale_year` somente para datas válidas.
- Mantive os 100 registros; não descartei vendas por causa de data inválida.

A base resultante está em [`dados_sanitizados.csv`](./dados_sanitizados.csv). O arquivo [`dados.json`](./dados.json) é o payload usado no HTML.

## Prompt usado e evolução

### Prompt inicial

> Crie uma dashboard em HTML com os dados sanitizados da planilha Porsche. Inclua total de vendas, receita e gráficos para modelo, ano, cidade e pagamento, além de filtros.

### O que estava faltando

Esse prompt gerava uma tela com muitos gráficos, mas não definia decisões, regras de qualidade, campos permitidos nem como lidar com datas inválidas. A primeira melhoria foi trocar “gráficos para tudo” por perguntas de negócio explícitas.

### Prompt final

> Você é um analista de vendas e designer de dashboards. Gere um único arquivo `index.html`, sem framework e sem build, em português do Brasil, usando somente os dados tratados fornecidos em JSON. Não use `customer_name`, `salesperson` nem qualquer coluna crua. Trate `price` e `vehicle_year` como números e não invente datas para registros com `sale_date` vazio. Crie uma dashboard responsiva, com visual executivo inspirado em um cockpit automotivo premium: fundo grafite, acentos dourado e ciano, tipografia sans-serif, alto contraste e cartões discretos. Responda estas perguntas: (1) qual modelo concentra a receita? Use barras ordenadas por receita; (2) em que ano a operação vendeu mais? Use vendas e receita por ano de venda válida; (3) quais meios de pagamento exigem mais atenção operacional? Mostre quantidade de vendas e status principal por meio de pagamento. Inclua filtros combináveis para modelo, cidade, ano do veículo, pagamento, estado e status. No topo, recalcule vendas filtradas, receita, ticket médio e entregas concluídas. Mostre estado vazio quando o filtro não tiver dados. Adicione um insight textual curto para o modelo líder e deixe todos os valores formatados em USD. Entregue também uma explicação do tratamento da base e um exemplo de filtro aplicado no README.

### Refinamentos feitos

- Troquei “ano” ambíguo por **ano do veículo** nos filtros e **ano da venda** na série temporal.
- Separei receita de volume para evitar conclusões erradas sobre o modelo mais rentável.
- Adicionei o recálculo de ticket médio e entregas concluídas para tornar filtros úteis para decisão.
- Incluí estado vazio e a regra explícita para `INVALID`.
- Removi dependências de framework para o arquivo ser realmente portátil.

## Exemplo de filtro aplicado

Selecione `Modelo = 911 Turbo S` e `Pagamento = Wire Transfer`. O topo e os três blocos mudam para esse recorte, permitindo comparar a receita desse modelo dentro desse método de pagamento com o total geral. Para voltar ao início, clique em **Limpar filtros**.

## Evidência de execução

A dashboard foi validada localmente com 100 registros embutidos, filtros combináveis e recálculo dos indicadores. Para publicar depois no GitHub Pages: crie um repositório público chamado, por exemplo, `porsche-sales-dashboard`, envie `index.html`, `README.md` e `dados_sanitizados.csv`, e ative Pages em **Settings → Pages → Deploy from branch → main → /root**.

A captura [`evidencia-dashboard.webp`](./evidencia-dashboard.webp) registra a tela inicial renderizada no navegador. O teste de filtro foi feito com `911 Turbo S` + `Wire Transfer`, resultando em 2 vendas e US$ 484.300.

## Ferramenta usada

Usei o caminho de construção direta em HTML local, equivalente ao fluxo de Canvas: primeiro tratei e delimitei os dados, depois escrevi o prompt orientado a perguntas e implementei uma versão determinística para preservar a precisão dos números. Não usei agente com skill.
