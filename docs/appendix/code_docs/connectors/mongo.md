# MongoDB Connector (`MongoConnect`)

!!! abstract "Class Overview"
    `MongoConnect` is a robust, resilient Python wrapper for `pymongo.MongoClient`. It implements a **Multiton Design Pattern** to manage database connections efficiently, ensuring that only a single active connection exists for any unique combination of `(URI, Database, Collection)`. 

## Core Architecture: The Multiton Pattern

Instead of creating a new database connection every time the class is instantiated, `MongoConnect` intercepts the creation process using the `__new__` dunder method. 

1. **State Check:** It checks an internal `_instances` dictionary for an existing connection matching the requested URI, DB, and Collection.
2. **Health Check:** If an instance exists, it actively pings the MongoDB server (`if_alive()`) to ensure the connection hasn't dropped or timed out.
3. **Smart Instantiation:** It only creates a new `MongoClient` connection if no instance exists, or if the existing connection is dead.

---

## Class Methods

### Connection Management

| Method | Description |
| :--- | :--- |
| `get_client()` | Returns the raw `pymongo.MongoClient` connection object. |
| `get_collection()` | Returns the specific `pymongo.collection.Collection` object instantiated for this class. |
| `if_alive()` | Pings the MongoDB `admin` database. Returns `True` if the connection is healthy, and `False` if it has dropped. |
| `_connect()` | An internal helper method to forcefully re-establish the connection to the URI, Database, and Collection. |

### Data Retrieval

!!! tip "Automatic ObjectId Serialization"
    Both fetch methods automatically convert MongoDB's native `_id` (which is an `ObjectId` object) into a standard Python `str`. This prevents serialization errors when passing this data to JSON-based REST APIs or frontend components.

* **`fetch_one(idx: str, filter={})`**
    * **Description:** Retrieves a single document where the document's `"id"` field matches the provided `idx` string. 
    * **Arguments:** * `idx` (str): The specific ID to search for.
        * `filter` (dict, optional): Additional query parameters to narrow the search.
    * **Returns:** A dictionary representing the document, or `None`.

* **`fetch_all(filter={}, limit=50)`**
    * **Description:** Retrieves multiple documents matching the filter. Results are automatically sorted by `_id` in descending order (newest first).
    * **Arguments:**
        * `filter` (dict, optional): Query parameters. Defaults to an empty dict (fetch all).
        * `limit` (int, optional): Maximum number of documents to return. Defaults to 50.
    * **Returns:** A list of document dictionaries.

### Data Mutation

* **`add_one(data: dict)`**
    * Inserts a single document dictionary into the collection.
* **`add_many(data: list)`**
    * Inserts a list of document dictionaries into the collection in a single batch operation.
* **`update_one(idx: str, data: dict, filter={})`**
    * Updates a single document where `"id"` matches `idx`. Uses the MongoDB `$set` operator to update only the fields provided in the `data` dictionary.
* **`update_many(data: dict)`**
    * Updates multiple documents. *(Note: Based on the current implementation, it specifically looks for documents matching `{"id": data["id"]}` and applies the `$set` operator).*

---

## Usage Example

```python
from your_module import MongoConnect

# 1. First instantiation (creates a new connection)
db_client = MongoConnect(
    uri="mongodb://localhost:27017",
    db="ikegai_core",
    collection_name="agent_memory"
)

# Insert data
db_client.add_one({"id": "agent_001", "status": "idle", "task": None})

# 2. Second instantiation elsewhere in the code (reuses the alive connection)
same_client = MongoConnect(
    uri="mongodb://localhost:27017",
    db="ikegai_core",
    collection_name="agent_memory"
)

# Fetch data (ObjectIds are cleanly converted to strings)
records = same_client.fetch_all(filter={"status": "idle"}, limit=10)
print(records)

```