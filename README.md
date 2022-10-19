## Challenge BI

Praticar o desenvolvimento de relatorios gerencias utilizando o Power BI

| :placard: Vitrine.Dev |     |
| -------------  | --- |
| :sparkles: Nome        | **Semana 2 - Marketing**
| :label: Tecnologias | Power BI
| :rocket: URL         | https://bit.ly/dashboarddemarketing
| :fire: Desafio     | https://www.alura.com.br/challenges/bi

<!-- Inserir imagem com a #vitrinedev ao final do link -->
![1632271385455](https://user-images.githubusercontent.com/62486279/134324531-32b01c0f-7804-432d-b80c-cb2c8811739a.jpg#vitrinedev)

## Detalhes do projeto

O segundo desafio da **Alura** através do Challenge BI (https://www.alura.com.br/challenges/bi) onde agora a **Alura Shop** (empresa fictícia) após uma campanha de publicidade, solicitou através da gerência de marketing o resultando da campanha através das seguintes métricas:

- Total de compras
- Total de valor convertido em compras
- Total do valor investido na campanha
- Custo por clique
- Jornada de compra
- Taxa de conversão
- Ticket médio por dispositivo
- Retorno do investimento em publicidade (ROAS) por idade e gênero
- Valor convertido em compras por dia

E como no outro desafio, tivemos as demandas e controle do projeto via Trello:

![image](https://user-images.githubusercontent.com/62486279/134324954-49173e92-5ec4-489a-89fe-dc03aa8e85db.png)

Link: https://trello.com/b/j0pq60Fd

Através de um sistema de cartão (Kanban)

## Como o report foi criado?

## 1) ETL e Relacionamento entre tabelas

Dessa vez, foram disponibilizado duas (2) bases em formato **.json**: 

![image](https://user-images.githubusercontent.com/62486279/134325201-d76a8b60-0b83-4582-bd18-d54ef2699fed.png)

Importanto as duas via Power Query e cada base possuindo as seguintes informações:

**Tabela_dispositivos**

![image](https://user-images.githubusercontent.com/62486279/134326056-b614d1ff-4b52-4e66-9bcf-97bd79507fcf.png)

![image](https://user-images.githubusercontent.com/62486279/134326107-ad6e55df-e661-40f6-a448-db53a5fde854.png)

![image](https://user-images.githubusercontent.com/62486279/134326149-12d54b19-1347-4022-af54-6bc868381984.png)

**Tabela_idade_e_genero**

![image](https://user-images.githubusercontent.com/62486279/134326240-3a6c9bd7-5024-493e-ad9a-24b0cc92c8e7.png)

![image](https://user-images.githubusercontent.com/62486279/134326272-a7346583-b2bf-4870-83a3-f7c2a7b4cfc3.png)

![image](https://user-images.githubusercontent.com/62486279/134326305-90c5b05a-f496-4bde-93b7-7e675b36433c.png)

E para auxiliar o projeto, foi criado algumas tabelas auxiliares:

**Calendario**

![image](https://user-images.githubusercontent.com/62486279/134327450-3fd009f5-9715-4629-a478-cf362cdffb5b.png)

**Genero**

![image](https://user-images.githubusercontent.com/62486279/134327656-0daf3209-66d5-4d14-b073-9bc9029dbdbb.png)

**Idade**

![image](https://user-images.githubusercontent.com/62486279/134327692-9457a8ce-c92a-4d12-8143-1be6f9e0b926.png)

**Plataforma do dispositivo**

![image](https://user-images.githubusercontent.com/62486279/134327728-82d85eba-ac8c-4c13-8c4a-6d3f7fe1e033.png)

Com as tabelas se relacionando da seguinte forma:

![image](https://user-images.githubusercontent.com/62486279/134327933-af21138a-d1c4-4e2a-bccc-aa853a85b676.png)

E para cada tabela, algumas tratativa foram realizando:

- Alterando na base **Tabela_dispositivos** e **Tabela_idade_e_genero** valores **null** para **0**
- Removido a coluna **FIELD4** da tabela **Tabela_idade_e_genero**
- Calendario criado de forma dinamica com base na tabela **Tabela_dispositivos**
- Adionado colunas na tabela **Calendario** para auxiliar a organização da tabela

## 2) Calculos 

Para o calcular as métricas, foram criado a seguintes medidas utilizando funções DAX

Medida   | Dax | Comentário
-------- | ---------- | ----------
Total Convertido | SUM(Tabela_idade_e_genero[Valor de conversão de compras])| Calcular o valor de conversão de compras
Ticket Medio | VAR Faturmento = <br/> CALCULATE( <br/> SUM(Tabela_dispositivos[Valor de conversão de compras]),<br/> Tabela_dispositivos[Plataforma do dispositivo]<>"All",Tabela_dispositivos[Plataforma]<>"ALL") <br/> VAR QtdCompras =  <br/> CALCULATE( <br/> SUM(Tabela_dispositivos[Compras]), <br/> Tabela_dispositivos[Compras])  <br/> RETURN  <br/> DIVIDE(Faturmento,QtdCompras)| Calcular o ticket médio
Total Carrinho - Dispositivo | SUM(Tabela_dispositivos[Adicionados ao carrinho])| Calcular o que foi adicionado ao carrinho 
Total Compras - Dispositivo | SUM(Tabela_dispositivos[Compras]) | Calcular as compras
Total Vizualizações - Dispositivo | SUM(Tabela_dispositivos[Visualizações por página])| Calcular a vizualização 
ROAS | VAR Faturamento = <br/> CALCULATE(<br/> SUM(Tabela_idade_e_genero[Valor de conversão de compras]),<br/> Tabela_idade_e_genero[Valor de conversão de compras]<>0)<br/> VAR Investimento = <br/> SUM(Tabela_idade_e_genero[Quantia gasta (BRL)])<br/> Return <br/> DIVIDE(Faturamento,Investimento)<br/> | Calcular o retorno do investimento em publicidade
Total Investido | SUM(Tabela_idade_e_genero[Quantia gasta (BRL)]) | Calcular quantidade investida 
Total Convertido| SUM(Tabela_idade_e_genero[Valor de conversão de compras]) | Calcular a quantidade convertida 
Total Compras | SUM(Tabela_idade_e_genero[Compras]) | Calcular a quantidade de compras 
Taxa de Conversão | VAR Vizualizacao = <br/> SUM(Tabela_idade_e_genero[Visualizações por página])<br/> VAR Conversao =<br/> SUM(Tabela_idade_e_genero[Compras])<br/> RETURN <br/> DIVIDE(Conversao,Vizualizacao) | Calcular a taxa de conversão
Custo por Clique | VAR valorin = <br/> [Total Investido]<br/> VAR numeroclique = <br/> SUM(Tabela_idade_e_genero[Cliques no link])<br/> RETURN <br/> DIVIDE(valorin,numeroclique)<br/> | Calcular o custo por clique

## Materiais de apoio 

**Livro:**

https://databinteligencia.com.br/produtos/dominando-o-power-bi/

**Sites:**

https://resultadosdigitais.com.br/blog/o-que-e-cpc-cpm-cpa/

https://cursos.alura.com.br/power-bi-gateway-de-dados-c259

https://www.alura.com.br/artigos/atualizando-relatorios-com-bi-service?utm_source=gnarus&utm_medium=timeline

https://leads2b.com/blog/ticket-medio/#:~:text=O%20ticket%20m%C3%A9dio%20%C3%A9%20uma%20m%C3%A9trica%20que%20fornece%20detalhes%20sobre,dividindo%20pelo%20n%C3%BAmero%20de%20clientes

https://neilpatel.com/br/blog/roas-o-que-e/

https://www.zendesk.com.br/blog/taxa-de-conversao/#:~:text=Para%20calcular%20a%20taxa%20de,de%20convers%C3%A3o%20%C3%A9%20de%2030%25

https://resultadosdigitais.com.br/blog/o-que-taxa-de-conversao/

**Videos:**

https://www.youtube.com/watch?v=_swQ5B9VF_8

https://www.youtube.com/watch?v=Y93cUZBA130&t=3487s
