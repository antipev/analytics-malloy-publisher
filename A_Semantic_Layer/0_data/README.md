
# Create DuckDB

`duckdb thelook.db <<EOF`
`CREATE TABLE distribution_centers AS SELECT * FROM read_parquet('/home/maxantipev/analytics-malloy-analysis/A_Semantic_Layer/0_sources/data/distribution_centers/*.parquet');`

`CREATE TABLE events AS SELECT * FROM read_parquet('/home/maxantipev/analytics-malloy-analysis/A_Semantic_Layer/0_sources/data/events/*.parquet');`

`CREATE TABLE inventory_items AS SELECT * FROM read_parquet('/home/maxantipev/analytics-malloy-analysis/A_Semantic_Layer/0_sources/data/inventory_items/*.parquet');`

`CREATE TABLE order_items AS SELECT * FROM read_parquet('/home/maxantipev/analytics-malloy-analysis/A_Semantic_Layer/0_sources/data/order_items/*.parquet');`

`CREATE TABLE products AS SELECT * FROM read_parquet('/home/maxantipev/analytics-malloy-analysis/A_Semantic_Layer/0_sources/data/products/*.parquet');`

`CREATE TABLE users AS SELECT * FROM read_parquet('/home/maxantipev/analytics-malloy-analysis/A_Semantic_Layer/0_sources/data/users/*.parquet');`

`.exit EOF`


# Run Malloy Publisher locally

## config that works locally


{
  "projects": [
    {
      "name": "theLook eCommerce - Unified Star Schema Model - DEMO",
      "connections": [
        {
          "name": "thelook",
          "type": "duckdb",
          "duckdbConnection": {
            "databasePath": "./B_Published_Semantic_Layer/0_data/thelook.duckdb"
          }
        }
      ],
      "packages": [
        {
          "name": "B_Published_Semantic_Layer",
          "location": "./B_Published_Semantic_Layer"
        }
      ]
    }
  ]
}

## 1. Stop the server
pkill -f malloy-publisher

## 2. Clean the environment (This deletes the 158MB copy in publisher_data)
rm -rf publisher_data/ publisher.db*

## 3. Re-create the folder structure the Publisher expects
mkdir -p "/home/maxantipev/analytics-malloy-publisher/publisher_data/theLook eCommerce - Unified Star Schema Model - DEMO/"

## 4. Manually copy the healthy 158MB database back into the Publisher's folder
cp /home/maxantipev/analytics-malloy-publisher/B_Published_Semantic_Layer/0_data/thelook.duckdb \
   "/home/maxantipev/analytics-malloy-publisher/publisher_data/theLook eCommerce - Unified Star Schema Model - DEMO/thelook.duckdb"

## 5. Start the server
npx @malloy-publisher/server --server_root .




# Run Malloy Publisher on Google Cloud Run
## config that works online


{
  "projects": [
    {
      "name": "theLook eCommerce - Unified Star Schema Model - DEMO",
      "connections": [
        {
          "name": "thelook",
          "type": "duckdb",
          "duckdbConnection": {
            "databasePath": "/app/B_Published_Semantic_Layer/0_data/thelook.duckdb"
          }
        }
      ],
      "packages": [
        {
          "name": "B_Published_Semantic_Layer",
          "location": "./B_Published_Semantic_Layer"
        }
      ]
    }
  ]
}

----
Laptop: /home/maxantipev/analytics-malloy-publisher/ = Container: /app/
----

DEMO online: 
https://malloy-publisher-bolcwt6srq-nn.a.run.app



## 1 Build from docker file

gcloud builds submit --tag gcr.io/my-1-st-project-training/malloy-publisher .
find publisher_data -maxdepth 4

## 2 Deploy to Cloud Run

gcloud run deploy malloy-publisher   --image gcr.io/my-1-st-project-training/malloy-publisher   --platform managed   --region northamerica-northeast1   --allow-unauthenticated   --memory 4Gi   --cpu 2   --cpu-boost   --timeout 600


## 3 Logs

gcloud logging read "resource.type=cloud_run_revision AND resource.labels.service_name=malloy-publisher" --limit 20 --format="table(timestamp, textPayload)"