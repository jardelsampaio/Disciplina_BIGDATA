# 🛒 Pipeline de Dados E-commerce Brasil

Pipeline moderno para ingestão e análise de pedidos de e-commerce no Brasil.  
O fluxo integra **Kafka**, **Spark** e **PostgreSQL** para transformar dados brutos de vendas em **insights acionáveis**.

---

## 🔎 Visão Geral

O objetivo é automatizar a jornada dos dados:

1. **Coleta** — pedidos extraídos da API da loja (ou dataset sintético incluído).  
2. **Ingestão** — dados enviados para o **Kafka**.  
3. **Processamento** — **Spark** organiza, enriquece e valida as mensagens.  
4. **Armazenamento** — registros consolidados no **PostgreSQL**.  
5. **Análise** — consultas SQL ou ferramentas de BI exploram os dados tratados.

```
Loja/API → Kafka → Spark → PostgreSQL → BI/Relatórios
```

---

## 📂 Estrutura do Projeto
```
├── data/                 
│   └── pedidos.csv            # Dados sintéticos de exemplo (50k linhas)
├── src/                      
│   ├── extract_orders.py      # Exemplo de extração (mock/API)
│   ├── spark_kafka_producer.py# Envia pedidos CSV para Kafka via Spark
│   ├── kafka_consumer.py      # Consome Kafka e grava no PostgreSQL
│   ├── postgres_query_tool.py # Consultas pré-definidas no PostgreSQL
│   ├── config.py              # Variáveis de conexão (Kafka, Postgres, API)
│   └── utils/
│       └── logger.py          # Logger padronizado
├── docker-compose.yaml        # Infra: Kafka, Zookeeper e Postgres
├── requirements.txt           # Dependências Python
└── README.md
```

---

## ⚙️ Pré-requisitos

- Python **3.8+**  
- Docker + Docker Compose  
- Spark instalado localmente  
- Cliente PostgreSQL (ou DBeaver, pgAdmin)  

---

## 🚀 Como Executar

### 1) Clonar e instalar dependências
```bash
git clone <url-do-repo>
cd pipeline_ecommerce
pip install -r requirements.txt
```

### 2) Subir Kafka e Postgres
```bash
docker-compose up -d
```
> Postgres padrão: `postgresql://app:app@localhost:5432/ecommerce`

### 3) Produzir mensagens no Kafka
```bash
spark-submit   --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.1   src/spark_kafka_producer.py   --csv data/pedidos.csv --topic pedidos_ecommerce
```

### 4) Consumir e gravar no Postgres
```bash
python src/kafka_consumer.py --topic pedidos_ecommerce --group ecommerce-consumers
```

### 5) Consultar os dados
```bash
# Top 10 produtos
python src/postgres_query_tool.py top-products --limit 10 --out top10_produtos.csv

# Top 10 cidades
python src/postgres_query_tool.py top-cities --limit 10 --out top10_cidades.csv
```

---

## 📊 Exemplos de Análises

- Produtos mais vendidos por região.  
- Receita acumulada por categoria.  
- Desempenho de canais de venda (Site, App, Marketplace).  
- Status dos pedidos (Pago, Pendente, Cancelado, Reembolsado).  

---

## 🛠️ Tecnologias

- **Python** – Scripts de extração, consumo e consultas.  
- **Apache Spark** – Processamento em lote e integração com Kafka.  
- **Apache Kafka** – Streaming de dados de pedidos.  
- **PostgreSQL** – Armazenamento analítico e consultas.  
- **Docker Compose** – Orquestração de Kafka, Zookeeper e Postgres.  

---

## 📌 Roadmap

- [ ] Implementar ingestão contínua com Spark Structured Streaming.  
- [ ] Criar dashboards em Power BI / Superset.  
- [ ] Adicionar camada de qualidade dos dados (validação e alerta).  
- [ ] Automatizar pipeline no Airflow.  

---

✨ Desenvolvido como projeto educacional para **engenharia de dados aplicada a e-commerce**.  
