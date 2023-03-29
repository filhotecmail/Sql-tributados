## Relatorio Calculo ICMS Estque Tributado


``` SQL
  WITH CTE AS ( 
  SELECT DISTINCT 
    P.ID_PRODUTO, 
    P.PRODUTO, 
    P.BARRAS, 
    SUM( P.ESTOQUE )QUANTIDADE, 
    SUM( D.ICMS_BC )ICMS_BC, D.NCM,    D.UNIDADE_COM, 
    D.CST, 
    D.ICMS_TAXA "ALIQ%", 
    SUM(( COALESCE(D.ICMS_BC,0.00) * D.ICMS_TAXA_ENTR / 100 ))ICMS_VALOR 
    FROM NOTA_COMPRA_DETALHE D JOIN NOTA_COMPRA C ON ( C.ID = D.ID_NFE ) JOIN PRODUTOS P 
    ON ( D.ID_PRODUTO = P.ID_PRODUTO ) JOIN FORNECEDOR F ON ( F.ID_FORNECEDOR = C.ID_FORNECEDOR ) 
    WHERE ( P.ST IN ( 'T' ) ) AND ( ( D.CST IN ( '000','020','102' ) ) ) 
    GROUP BY 
       P.ID_PRODUTO, 
       P.PRODUTO, 
       P.BARRAS, 
       D.NCM, 
       D.UNIDADE_COM, 
       D.CST, 
       D.ICMS_TAXA ) 

SELECT C.ID_PRODUTO,
       C.PRODUTO, 
       C.BARRAS, 
       C.QUANTIDADE, 
       SUM( C.ICMS_BC / C.QUANTIDADE ), 
       SUM( C.ICMS_VALOR / C.QUANTIDADE ),        C.NCM, 
       C.UNIDADE_COM, 
       C.CST, 
       C."ALIQ%" FROM CTE C GROUP BY C.ID_PRODUTO, 
       C.PRODUTO, 
       C.BARRAS, 
       C.QUANTIDADE, 
       C.NCM, 
       C.UNIDADE_COM, 
       C.CST, 
       C."ALIQ%"
```  
  


Este é um SQL que realiza uma consulta em um banco de dados e retorna informações sobre os produtos que foram comprados de fornecedores. O SQL é composto por duas partes: a primeira usa a cláusula "WITH" para criar uma expressão de tabela comum (CTE), que é uma tabela temporária usada na segunda parte do SQL.

A CTE é criada com os seguintes campos:

-   ID_PRODUTO: identificador do produto;
-   PRODUTO: nome do produto;
-   BARRAS: código de barras do produto;
-   QUANTIDADE: quantidade total do produto em estoque;
-   ICMS_BC: base de cálculo do ICMS (Imposto sobre Circulação de Mercadorias e Serviços);
-   NCM: Nomenclatura Comum do Mercosul, que é uma classificação internacional de produtos;
-   UNIDADE_COM: unidade de medida do produto;
-   CST: código de situação tributária do produto;
-   "ALIQ%": alíquota do ICMS em percentual;
-   ICMS_VALOR: valor total do ICMS a ser pago.

A segunda parte do SQL seleciona informações da CTE criada anteriormente e agrupa os resultados por produto. Os campos selecionados nesta parte são:

-   ID_PRODUTO, PRODUTO, BARRAS: identificador, nome e código de barras do produto;
-   QUANTIDADE: quantidade total do produto em estoque;
-   ICMS_BC: base de cálculo do ICMS;
-   ICMS_VALOR: valor total do ICMS a ser pago;
-   NCM: Nomenclatura Comum do Mercosul;
-   UNIDADE_COM: unidade de medida do produto;
-   CST: código de situação tributária do produto;
-   "ALIQ%": alíquota do ICMS em percentual.

A diferença em relação à primeira parte do SQL é que agora os valores de ICMS_BC e ICMS_VALOR são divididos pela quantidade de produtos em estoque, a fim de obter uma média por unidade. Isso é feito usando a função "SUM" junto com a cláusula "GROUP BY", que agrupa os resultados por produto.

### Resultado

![image](https://user-images.githubusercontent.com/18727307/228546776-b65e7c10-bd25-455d-8768-44753ab61318.png)
 
