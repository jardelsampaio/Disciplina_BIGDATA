# Pipeline de Dados E-commerce

Esse projeto foi feito pra simular um pipeline de dados de pedidos de e-commerce.  
A ideia é pegar dados (CSV ou API), jogar no Kafka, processar com Spark e gravar tudo no Postgres.  
Depois dá pra consultar e analisar os pedidos.

---

## Estrutura

```
├── data/
│   └── pedidos.csv            # Dataset sintético (~50k linhas) que gerei pra testes
├── src/
│   ├── extract_orders.py      # Exemplo de extração (mock/API)
│   ├── spark_kafka_producer.py# Envia pedidos pro Kafka usando Spark
│   ├── kafka_consumer.py      # Lê do Kafka e grava no Postgres
│   ├── postgres_query_tool.py # Script de consulta no Postgres
│   ├── config.py              # Onde ficam as configs de conexão
│   └── utils/logger.py        # Logger básico
├── docker-compose.yaml        # Sobe Kafka, Zookeeper e Postgres
├── requirements.txt
└── README.md
```

---

## O que usei

- Python 3.10 (rodei no Ubuntu 22.04 e também no Windows com WSL)  
- Docker Desktop pra subir Kafka/Zookeeper/Postgres  
- Spark 3.5.1 local  
- PostgreSQL 15 (via container do docker-compose)

---

## Como rodei aqui

1. Clonei o projeto e instalei as libs:
   ```bash
   pip install -r requirements.txt
   ```

2. Subi os serviços:
   ```bash
   docker-compose up -d
   ```
   > Postgres ficou disponível em: `postgresql://app:app@localhost:5432/ecommerce`

3. Testei o dataset sintético (`data/pedidos.csv`).  
   Ele tem colunas tipo `pedido_id, cliente_id, produto_id, categoria, qtde, valor_total, uf, cidade...`

4. Mandei os pedidos pro Kafka via Spark:
   ```bash
   spark-submit      --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.1      src/spark_kafka_producer.py      --csv data/pedidos.csv --topic pedidos_ecommerce
   ```

5. Rodei o consumer pra salvar no Postgres:
   ```bash
   python src/kafka_consumer.py --topic pedidos_ecommerce --group ecommerce-consumers
   ```

6. Fiz umas consultas rápidas:
   ```bash
   python src/postgres_query_tool.py top-products --limit 5
   python src/postgres_query_tool.py top-cities --limit 5
   ```

   Exemplo de saída real (top produtos):
   ```
   produto_id | pedidos | receita
   -----------+---------+---------
   P-000123   |   350   | 420000.50
   P-004567   |   210   | 175000.90
   ...
   ```

---

## O que dá pra melhorar

- Rodar isso em **streaming contínuo** com Spark Structured Streaming (aqui tá batch).  
- Criar uns dashboards (quero testar no Superset ou Power BI).  
- Adicionar umas regras de **qualidade de dados** (ex.: não deixar qtde negativa, status inválido etc).  
- Automatizar com **Airflow** em vez de rodar os scripts na mão.

---

## Observações minhas

- O CSV sintético ficou grandinho (50k linhas), mas roda tranquilo no Spark local.  
- Precisei ajustar a versão do pacote Kafka no `spark-submit` pra bater com a versão do meu Spark (fica a dica).  
- Os scripts são bem simples, dá pra expandir fácil.

---

✨ Projeto que montei pra treinar engenharia de dados aplicada a e-commerce.
