Candidato: Renan de Souza Correa

- Qual o objetivo do comando cache em Spark?
O uso do comando cache ajuda a melhorar a eficiência do código nesse tipo de cenário, pois permite que resultados intermediários de operações lentas possam ser armazenados e reutilizados repetidamente.

- O mesmo código implementado em Spark é normalmente mais rápido que a implementação equivalente em MapReduce. Por quê?
Existem alguns fatores no desenho dessas ferramentas que tornam as aplicações desenvolvidas em MapReduce geralmente mais lentas doque as que utilizam Spark. Um desses fatores é o uso de memória. É comum a necessidade de rodar vários jobs MapReduce em sequência em vez de um único job. Ao usar MapReduce, o resultado de cada job é escrito em disco, e precisa ser lido novamente do disco quando passado ao job seguinte. Spark, por outro lado, permite que resultados intermediários sejam passados diretamente entre as operações a serem executadas através do armazenamento em memória, ou até mesmo que diversas operações possam ser executadas sobre um mesmo conjunto de dados em cache, reduzindo a necessidade de escrita/leitura em disco. Adicionalmente, mesmo em cenários onde ocorre a execução de apenas um job, o uso de Spark tende a ter desempenho superior ao MapReduce. Isso ocorre porque jobs Spark podem ser iniciados mais rapidamente, pois para cada job MapReduce uma nova instância da JVM é iniciada, enquanto Spark mantém a JVM em constantemente em execução em cada nó, precisando apenas iniciar uma nova thread, que é um processo extremamente mais rápido.

- Qual é a função do SparkContext ?
O SparkContext funciona como um cliente do ambiente de execução Spark. Através dele, passam-se as configurações que vão ser utilizadas na alocação de recursos, como memória e processadores, pelos executors. Também usa-se o SparkContext para criar RDDs, colocar jobs em execução, criar variáveis de broadcast e acumuladores.

- Explique com suas palavras o que é Resilient Distributed Datasets(RDD)?
RDDs são a principal abstração de dados do Spark. Eles são chamados Resilient por serem tolerantes à falha, isto é, são capazes de recomputar partes de dados perdidas devido a falhas nos “nós” e são Distributed porque podem estar divididos em partições através de diferentes nós em um cluster. 

- GroupByKey é menos eficiente que reduceByKey em grandes dataset.  Por quê?
Quando fazendo uma agregação utilizando reduceByKey o “Spark” sabe que pode realizar a operação passada como parâmetro em todos os elementos de mesma chave em cada partição para obter um resultado parcial antes de passar esses dados para os executores que vão calcular o resultado final, resultando em um conjunto menor de dados sendo transferido. Por outro lado, ao usar groupByKey e aplicar a agregação em seguida, o cálculo de resultados parciais não é realizado, dessa forma um volume muito maior de dados é desnecessariamente transferido através dos executores podendo até ser maior que a quantidade de memória disponível para o mesmo, o que cria a necessidade de escrita dos dados em disco e resulta em um grande impacto negativo na performance

- Explique o que o código Scala abaixo faz?
1. val textFile = sc . textFile ( "hdfs://..." )
2. val counts = textFile . flatMap ( line => line . split ( " " ))
3.           . map ( word => ( word , 1 ))
4.           . reduceByKey ( _ + _ )
5. counts . saveAsTextFile ( "hdfs://..." )

1= Carrega um arquivo que está presente no hdfs (Hadoop Distributed File System) e atribui para o objeto textFile, que é do tipo RDD.
2= Cada linha é "quebrada" em uma sequência de palavras e as sequencias correspondentes a cada linha são transformadas em uma única coleção de palavras.
3= Ocorre o mapeamento de cada palavra, para um par de "chave: valor".
4= Nesta linha é feito o processo de redução por chave. O resultado final desse processo é um par de "chave: quantidade de aparições da palavra no objeto textFile".
5= Salva o objeto criado no procedimento de mapeamento e redução como um arquivo de texto no hdfs.

- QUESTÕES HTTP requests to the NASA Kennedy Space Center WWW server: 
1. Número de hosts únicos.
2. O total de erros 404.
3. Os 5 URLs que mais causaram erro 404.
4. Quantidade de erros 404 por dia.
5. O total de bytes retornados.

1= hostsUnique = spSession.sql("SELECT COUNT (DISTINCT HOST) as COUNT_UNIQUE_HOSTS FROM dataset").collect()
print("Quantidade de Hosts únicos: {}".format(hostsUnique[0]))

Quantidade de Hosts únicos: 137979

2= totalErros404WithSQL = spSession.sql("SELECT COUNT(COD_HTTP) as COUNT_HTTP_ERROR FROM dataset WHERE COD_HTTP = 404").first()
print("Total de erros'404': {}".format(totalErros404WithSQL[0]))

Total de erros'404': 20871

3= url404 = spSession.sql("SELECT HOST, COUNT(COD_HTTP) FROM dataset WHERE COD_HTTP = 404 GROUP BY HOST ORDER BY COUNT(COD_HTTP) DESC").take(10)
print("As 5 URLs que mais causaram erro 404:\n")

for i in range(0,5):
    print("{}º: url:'{}' com '{}' erros 404\n".format((i+1), url404[i]["HOST"], url404[i]["count(COD_HTTP)"]))

As 5 URLs que mais causaram erro 404:

1º: url:'hoohoo.ncsa.uiuc.edu' com '251' erros 404

2º: url:'piweba3y.prodigy.com' com '156' erros 404

3º: url:'jbiagioni.npt.nuwc.navy.mil' com '132' erros 404

4º: url:'piweba1y.prodigy.com' com '114' erros 404

5º: url:'www-d4.proxy.aol.com' com '91' erros 404

4= dia404 = spSession.sql("SELECT DAY, COUNT(COD_HTTP) FROM dataset WHERE COD_HTTP = 404 GROUP BY DAY ORDER BY DAY ASC").collect()

[Row(DAY='01', count(COD_HTTP)=559),
 Row(DAY='02', count(COD_HTTP)=291),
 Row(DAY='03', count(COD_HTTP)=773),
 Row(DAY='04', count(COD_HTTP)=705),
 Row(DAY='05', count(COD_HTTP)=733),
 Row(DAY='06', count(COD_HTTP)=1013),
 Row(DAY='07', count(COD_HTTP)=1106),
 Row(DAY='08', count(COD_HTTP)=692),
 Row(DAY='09', count(COD_HTTP)=627),
 Row(DAY='10', count(COD_HTTP)=704),
 Row(DAY='11', count(COD_HTTP)=734),
 Row(DAY='12', count(COD_HTTP)=666),
 Row(DAY='13', count(COD_HTTP)=747),
 Row(DAY='14', count(COD_HTTP)=698),
 Row(DAY='15', count(COD_HTTP)=581),
 Row(DAY='16', count(COD_HTTP)=516),
 Row(DAY='17', count(COD_HTTP)=677),
 Row(DAY='18', count(COD_HTTP)=721),
 Row(DAY='19', count(COD_HTTP)=847),
 Row(DAY='20', count(COD_HTTP)=740),
 Row(DAY='21', count(COD_HTTP)=637),
 Row(DAY='22', count(COD_HTTP)=476),
 Row(DAY='23', count(COD_HTTP)=578),
 Row(DAY='24', count(COD_HTTP)=748),
 Row(DAY='25', count(COD_HTTP)=876),
 Row(DAY='26', count(COD_HTTP)=702),
 Row(DAY='27', count(COD_HTTP)=703),
 Row(DAY='28', count(COD_HTTP)=504),
 Row(DAY='29', count(COD_HTTP)=420),
 Row(DAY='30', count(COD_HTTP)=571),
 Row(DAY='31', count(COD_HTTP)=526)]

5= totalBytes = spSession.sql("SELECT SUM(TOTAL_BYTES) as TOTAL_BYTES FROM dataset").first()
print("Total de bytes: {}".format(totalBytes[0]))

Total de bytes: 65524319796
