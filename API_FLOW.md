# MySQL AI Query Service - API Flow Documentation

This document explains the typical usage flow and patterns for integrating with the MySQL AI Query Service.

## 📋 Table of Contents

1. [Basic Flow](#basic-flow)
2. [Authentication & Sessions](#authentication--sessions)
3. [Complete Usage Example](#complete-usage-example)
4. [Advanced Patterns](#advanced-patterns)
5. [Error Handling](#error-handling)
6. [Best Practices](#best-practices)

## 🔄 Basic Flow

The typical interaction flow with the MySQL AI Query Service follows these steps:

```
┌─────────────────┐
│  1. Connect to  │
│    Database     │
└────────┬────────┘
         │
┌────────▼────────┐
│ 2. Get Tables   │
│   (Optional)    │
└────────┬────────┘
         │
┌────────▼────────┐
│ 3. Process      │
│ Table Schema    │
│   (Optional)    │
└────────┬────────┘
         │
┌────────▼────────┐
│ 4. Execute      │
│ Natural Language│
│    Queries      │
└────────┬────────┘
         │
┌────────▼────────┐
│ 5. Disconnect   │
│   (Optional)    │
└─────────────────┘
```

## 🔐 Authentication & Sessions

The service uses session-based authentication. When you connect to a database, a session is created and maintained through cookies.

### Session Management

```python
import requests

# Create a session object to persist cookies
session = requests.Session()

# All subsequent requests will maintain the session
response = session.post('http://localhost:5000/api/connect', json={...})
```

### Important Notes:
- Sessions expire after 1 hour of inactivity
- Always use the same session object for all requests
- Sessions are isolated - each session has its own database connection

## 📚 Complete Usage Example

Here's a complete example showing the full API flow:

### Step 1: Connect to Database

```python
import requests
import json

# Create session
session = requests.Session()
base_url = "http://localhost:5000"

# Connect to database
connection_response = session.post(f"{base_url}/api/connect", json={
    "host": "localhost",
    "user": "root",
    "password": "password",
    "database": "ecommerce"
})

if connection_response.status_code == 200:
    print("✅ Connected successfully")
else:
    print("❌ Connection failed:", connection_response.json())
    exit(1)
```

### Step 2: Explore Available Tables

```python
# Get list of tables
tables_response = session.get(f"{base_url}/api/get_tables")
tables = tables_response.json()["tables"]

print(f"Available tables: {tables}")
# Output: Available tables: ['users', 'orders', 'products', 'categories']
```

### Step 3: Process Table Schema (Recommended)

```python
# Process important tables for better query understanding
for table in ['users', 'orders', 'products']:
    process_response = session.post(f"{base_url}/api/process_table", json={
        "table_name": table
    })
    print(f"Processed table {table}: {process_response.json()['success']}")
```

### Step 4: Execute Natural Language Queries

```python
# Example queries
queries = [
    {
        "question": "How many active users do we have?",
        "table_name": "users"
    },
    {
        "question": "What were the top 5 selling products last month?",
        "table_name": "products"
    },
    {
        "question": "Show me orders over $1000 from this week",
        "table_name": "orders"
    }
]

for query in queries:
    response = session.post(f"{base_url}/api/query", json=query)
    result = response.json()
    
    print(f"\n📊 Question: {query['question']}")
    print(f"🔍 Generated SQL: {result['generated_query']}")
    print(f"📈 Results: Found {result['row_count']} rows")
    print(f"💡 AI Summary: {result['ai_summary']}")
```

### Step 5: Performance Optimization (Optional)

```python
# Check current performance settings
perf_response = session.get(f"{base_url}/api/performance_config")
print("Current settings:", perf_response.json())

# Enable high-performance mode
session.post(f"{base_url}/api/performance_config", json={
    "use_faster_model": True,
    "reduce_ai_context": True,
    "enable_vector_search": False
})
```

### Step 6: Disconnect

```python
# Clean up when done
disconnect_response = session.post(f"{base_url}/api/disconnect")
print("Disconnected:", disconnect_response.json()['message'])
```

## 🚀 Advanced Patterns

### Pattern 1: Batch Processing

```python
def batch_query(session, questions_and_tables):
    """Execute multiple queries efficiently"""
    results = []
    
    for question, table in questions_and_tables:
        response = session.post(f"{base_url}/api/query", json={
            "question": question,
            "table_name": table
        })
        results.append(response.json())
    
    return results

# Usage
queries = [
    ("Count users by country", "users"),
    ("Average order value by month", "orders"),
    ("Product inventory levels", "products")
]

results = batch_query(session, queries)
```

### Pattern 2: Connection Pool Management

```python
class DatabaseQueryClient:
    def __init__(self, base_url):
        self.base_url = base_url
        self.sessions = {}
    
    def get_session(self, connection_id):
        """Get or create session for a connection"""
        if connection_id not in self.sessions:
            self.sessions[connection_id] = requests.Session()
        return self.sessions[connection_id]
    
    def connect(self, connection_id, db_config):
        """Connect to database with specific ID"""
        session = self.get_session(connection_id)
        return session.post(f"{self.base_url}/api/connect", json=db_config)
    
    def query(self, connection_id, question, table):
        """Execute query on specific connection"""
        session = self.get_session(connection_id)
        return session.post(f"{self.base_url}/api/query", json={
            "question": question,
            "table_name": table
        })

# Usage for multi-tenant scenarios
client = DatabaseQueryClient("http://localhost:5000")

# Connect to different databases
client.connect("tenant1", {"database": "tenant1_db", ...})
client.connect("tenant2", {"database": "tenant2_db", ...})

# Query different databases
result1 = client.query("tenant1", "Show all users", "users")
result2 = client.query("tenant2", "Show all orders", "orders")
```

### Pattern 3: Retry Logic

```python
import time
from typing import Optional

def query_with_retry(session, question: str, table: str, 
                    max_retries: int = 3) -> Optional[dict]:
    """Execute query with automatic retry on failure"""
    
    for attempt in range(max_retries):
        try:
            response = session.post(f"{base_url}/api/query", json={
                "question": question,
                "table_name": table
            })
            
            if response.status_code == 200:
                return response.json()
            
            # If it's a connection error, try to reconnect
            if response.status_code == 401:
                # Reconnect logic here
                pass
                
        except requests.exceptions.RequestException as e:
            print(f"Attempt {attempt + 1} failed: {e}")
            
        if attempt < max_retries - 1:
            time.sleep(2 ** attempt)  # Exponential backoff
    
    return None
```

## ❌ Error Handling

### Common Error Responses

1. **No Database Connection (401)**
```json
{
    "error": "No active database connection. Please connect to a database first."
}
```

2. **Invalid Credentials (401)**
```json
{
    "error": "Access denied. Please check your username and password."
}
```

3. **Database Not Found (404)**
```json
{
    "error": "Database 'nonexistent' does not exist."
}
```

4. **Query Generation Failed (500)**
```json
{
    "error": "Failed to generate valid SQL query after multiple attempts",
    "suggestion": "Please try rephrasing your question or check the table schema"
}
```

### Error Handling Pattern

```python
def safe_query(session, question, table):
    """Execute query with comprehensive error handling"""
    try:
        response = session.post(f"{base_url}/api/query", json={
            "question": question,
            "table_name": table
        })
        
        if response.status_code == 200:
            return response.json()
        
        error_data = response.json()
        
        if response.status_code == 401:
            # Handle authentication errors
            print("Authentication error:", error_data['error'])
            # Attempt to reconnect
            
        elif response.status_code == 400:
            # Handle bad requests
            print("Query error:", error_data['error'])
            if 'suggestion' in error_data:
                print("Suggestion:", error_data['suggestion'])
                
        elif response.status_code == 500:
            # Handle server errors
            print("Server error:", error_data['error'])
            
    except requests.exceptions.ConnectionError:
        print("Connection error: Cannot reach the service")
    except requests.exceptions.Timeout:
        print("Timeout error: Request took too long")
    except Exception as e:
        print(f"Unexpected error: {e}")
    
    return None
```

## 📏 Best Practices

### 1. Always Process Tables Before Querying

Processing tables improves query accuracy:

```python
# Process table once after connecting
session.post(f"{base_url}/api/process_table", json={"table_name": "users"})

# Now queries will be more accurate
session.post(f"{base_url}/api/query", json={
    "question": "Show users who joined last month",
    "table_name": "users"
})
```

### 2. Use Specific Questions

❌ **Bad**: "Show data"  
✅ **Good**: "Show all active users who made purchases in the last 30 days"

❌ **Bad**: "Get orders"  
✅ **Good**: "Get orders with total amount over $100 from Q4 2023"

### 3. Optimize for Performance

For high-throughput scenarios:

```python
# Configure for performance
session.post(f"{base_url}/api/performance_config", json={
    "use_faster_model": True,
    "reduce_ai_context": True,
    "enable_vector_search": False,
    "enable_query_validation": False
})
```

### 4. Handle Result Limits

Be aware of result limits:

```python
result = session.post(f"{base_url}/api/query", json=query).json()

if result.get('limited'):
    print(f"⚠️ Results limited to {result['max_results']} rows")
    print(f"Total available: {result.get('total_count', 'Unknown')}")
```

### 5. Monitor Health

Regularly check service health:

```python
health = session.get(f"{base_url}/health").json()

if health['status'] != 'healthy':
    print("⚠️ Service degraded:", health)
```

### 6. Use Connection Pooling

For production applications:

```python
from concurrent.futures import ThreadPoolExecutor
import threading

class QueryPool:
    def __init__(self, base_url, pool_size=5):
        self.base_url = base_url
        self.pool = ThreadPoolExecutor(max_workers=pool_size)
        self.local = threading.local()
    
    def get_session(self):
        if not hasattr(self.local, 'session'):
            self.local.session = requests.Session()
        return self.local.session
    
    def query_async(self, question, table):
        return self.pool.submit(self._query, question, table)
    
    def _query(self, question, table):
        session = self.get_session()
        return session.post(f"{self.base_url}/api/query", json={
            "question": question,
            "table_name": table
        })
```

## 📊 Response Structure Reference

### Successful Query Response

```json
{
    "success": true,
    "question": "Original question",
    "generated_query": "SELECT * FROM users WHERE...",
    "results": [
        {"id": 1, "name": "John", ...},
        {"id": 2, "name": "Jane", ...}
    ],
    "row_count": 2,
    "total_count": 100,
    "limited": false,
    "max_results": 10000,
    "ai_summary": "<p>Summary of results...</p>",
    "performance": {
        "execution_time": "0.45 seconds",
        "timing_breakdown": {
            "sql_generation": "0.25s",
            "query_execution": "0.15s",
            "ai_response": "0.05s",
            "total": "0.45s"
        }
    }
}
```

---

For more examples and detailed API documentation, see the main [README.md](README.md). 