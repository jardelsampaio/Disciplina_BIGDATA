# Projeto: Pipeline de Dados E-commerce Brasil

Este projeto implementa um pipeline de dados para coletar, processar, transmitir e consultar informações de pedidos de um e-commerce, utilizando **Python, Apache Kafka, Apache Spark e PostgreSQL**.

## Estrutura do Projeto
```
├── data/
│   └── pedidos.csv                 # Dados históricos extraídos da API da loja
├── src/
│   ├── extract_orders.py            # Extrai pedidos da API da loja e salva em CSV
│   ├── spark_kafka_producer.py      # Processa dados com Spark e envia para o Kafka
│   ├── kafka_consumer.py            # Consome dados do Kafka e armazena no PostgreSQL
│   ├── postgres_query_tool.py       # Ferramenta de consulta aos dados no PostgreSQL
│   ├── config.py                    # Configurações de conexão (API, Kafka, PostgreSQL)
│   └── utils/
│       └── logger.py                # Utilitário de logging
├── requirements.txt                 # Dependências Python
├── docker-compose.yaml              # Sobe Kafka, Zookeeper e PostgreSQL via Docker
└── README.md                        # Este arquivo
```

## Pré-requisitos
- Python **3.8+**
- Docker e Docker Compose
- Instância PostgreSQL (é criada via docker-compose)

## Instalação
```bash
git clone <url-do-repo>
cd pipeline_ecommerce
pip install -r requirements.txt
```

## Subir infraestrutura (Kafka/Zookeeper/Postgres)
```bash
docker-compose up -d
```

## Pipeline — Passo a passo
1. **(Opcional)** Extrair pedidos reais da API da loja (exemplo no `extract_orders.py`)  
   > Neste bundle já existe um `data/pedidos.csv` sintético para testes.
2. **Enviar para Kafka via Spark (batch)**  
```bash
spark-submit   --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.1   src/spark_kafka_producer.py   --csv data/pedidos.csv --topic pedidos_ecommerce
```
3. **Consumir do Kafka e gravar no PostgreSQL**
```bash
python src/kafka_consumer.py --topic pedidos_ecommerce --group ecommerce-consumers
```
4. **Consultar no PostgreSQL**
```bash
python src/postgres_query_tool.py top-products --limit 10 --out top10_produtos.csv
```

## Notas
- Ajuste a versão do pacote Kafka no `spark-submit` conforme sua versão do Spark.
- A conexão padrão do Postgres (docker-compose) é: `postgresql://app:app@localhost:5432/ecommerce`.
- Variáveis podem ser configuradas via `.env` ou `src/config.py`.
