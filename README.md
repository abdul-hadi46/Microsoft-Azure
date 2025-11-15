import logging
import azure.functions as func
import json
from azure.cosmos import CosmosClient

def main(blob: func.InputStream):
    logging.info(f"Processing file: {blob.name}")

    content = blob.read().decode("utf-8")

    # Connect to Cosmos DB
    client = CosmosClient("<COSMOS_URI>", "<KEY>")
    database = client.get_database_client("filedb")
    container = database.get_container_client("filedetails")

    # Store data
    item = {
        "id": blob.name.replace("/", "-"),
        "filename": blob.name,
        "content": content,
        "length": len(content)
    }

    container.create_item(item)
    logging.info("Data stored successfully!")
