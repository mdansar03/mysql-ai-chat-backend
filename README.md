# MySQL AI Query Service

A production-ready, generic REST API service that enables natural language querying of MySQL databases using OpenAI and vector embeddings. Transform any MySQL database into an AI-powered conversational interface.

## 🎯 Overview

MySQL AI Query Service is a standalone backend service that provides:
- **Natural Language to SQL** conversion using OpenAI GPT models
- **Intelligent Query Generation** with context awareness
- **Vector-based Schema Understanding** using Pinecone
- **RESTful API** for easy integration with any frontend
- **Session-based Connection Management** for multi-tenant usage
- **Performance Optimization** features for production deployments

Perfect for building:
- 🤖 Database chatbots
- 📊 Business intelligence tools
- 🔍 Data exploration interfaces
- 📱 Mobile apps with database access
- 🌐 SaaS products with natural language data access

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Client Applications                       │
│  (Web Apps, Mobile Apps, CLI Tools, Jupyter Notebooks, etc.)   │
└────────────────────────┬────────────────────────────────────────┘
                         │ HTTPS/REST API
┌────────────────────────▼────────────────────────────────────────┐
│                    MySQL AI Query Service                        │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                   Flask REST API Layer                   │   │
│  │  • CORS-enabled endpoints                              │   │
│  │  • Session management                                  │   │
│  │  • Request validation                                  │   │
│  └─────────────────────┬───────────────────────────────────┘   │
│  ┌─────────────────────▼───────────────────────────────────┐   │
│  │              Query Processing Engine                     │   │
│  │  • Natural language understanding                       │   │
│  │  • SQL generation & validation                         │   │
│  │  • Performance optimization                            │   │
│  └──────┬──────────────────────────────────┬──────────────┘   │
│         │                                  │                    │
│  ┌──────▼──────────┐            ┌─────────▼──────────┐        │
│  │  OpenAI GPT     │            │  Pinecone Vector   │        │
│  │  • GPT-4/3.5    │            │  • Schema storage  │        │
│  │  • Embeddings   │            │  • Similarity search│        │
│  └─────────────────┘            └────────────────────┘        │
└────────────────────────┬────────────────────────────────────────┘
                         │ MySQL Protocol
         ┌───────────────▼───────────────────────┐
         │          MySQL Databases              │
         │  • Any MySQL 5.7+ compatible DB      │
         │  • MariaDB, AWS RDS, Google Cloud SQL │
         └───────────────────────────────────────┘
```

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- MySQL 5.7+ (or compatible)
- OpenAI API key
- Pinecone API key

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/mysql-ai-service.git
cd mysql-ai-service

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Create .env file
cp .env.example .env
# Edit .env with your API keys
```

### Environment Variables

Create a `.env` file with the following variables:

```env
# Required: OpenAI Configuration
OPENAI_API_KEY=sk-your-openai-api-key-here

# Required: Pinecone Configuration  
PINECONE_API_KEY=your-pinecone-api-key-here

# Optional: Flask Configuration
FLASK_SECRET_KEY=your-secret-key-here  # Auto-generated if not provided

# Optional: Performance Settings
MAX_QUERY_RESULTS=10000  # Maximum rows to return (default: 10000)
DEFAULT_QUERY_LIMIT=1000  # Default limit if not specified (default: 1000)
```

### Running the Service

```bash
# Development mode
python app.py

# Production mode with Gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 app:app

# Production mode with Waitress (Windows compatible)
waitress-serve --port=5000 app:app
```

The service will be available at `http://localhost:5000`

## 📡 API Documentation

### Base URL
```
http://localhost:5000/api
```

### Authentication
The service uses session-based authentication. Include credentials with requests to maintain sessions.

### Endpoints

#### 1. Connect to Database
Establish a connection to a MySQL database.

**Endpoint:** `POST /api/connect`

**Request:**
```json
{
  "host": "localhost",
  "user": "root",
  "password": "password",
  "database": "mydatabase"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Connected to database 'mydatabase' successfully",
  "database": "mydatabase"
}
```

**Error Responses:**
- `401`: Invalid credentials
- `404`: Database not found
- `503`: Cannot connect to server

---

#### 2. Disconnect from Database
Close the current database connection.

**Endpoint:** `POST /api/disconnect`

**Response:**
```json
{
  "success": true,
  "message": "Disconnected from database successfully"
}
```

---

#### 3. Check Connection Status
Get the current connection status.

**Endpoint:** `GET /api/connection_status`

**Response:**
```json
{
  "connected": true,
  "database": "mydatabase",
  "message": "Connected to 'mydatabase'"
}
```

---

#### 4. Get Available Tables
List all tables in the connected database.

**Endpoint:** `GET /api/get_tables`

**Response:**
```json
{
  "tables": ["users", "orders", "products"],
  "database": "mydatabase",
  "connection_id": "a1b2c3d4..."
}
```

---

#### 5. Process Table Schema
Analyze and store table schema for improved query generation.

**Endpoint:** `POST /api/process_table`

**Request:**
```json
{
  "table_name": "users"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Table users processed and stored successfully"
}
```

---

#### 6. Execute Natural Language Query
Convert natural language to SQL and execute.

**Endpoint:** `POST /api/query`

**Request:**
```json
{
  "question": "Show me all users who signed up last month",
  "table_name": "users"
}
```

**Response:**
```json
{
  "success": true,
  "question": "Show me all users who signed up last month",
  "generated_query": "SELECT * FROM users WHERE created_at >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH)",
  "results": [
    {
      "id": 1,
      "name": "John Doe",
      "email": "john@example.com",
      "created_at": "2024-01-15"
    }
  ],
  "row_count": 1,
  "total_count": 1,
  "limited": false,
  "max_results": 10000,
  "ai_summary": "<p>I found 1 user who signed up last month. John Doe joined on January 15, 2024.</p>",
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

#### 7. Performance Configuration
Get or update performance settings.

**Endpoint:** `GET /api/performance_config`

**Response:**
```json
{
  "enable_vector_search": false,
  "enable_performance_analysis": false,
  "enable_query_validation": false,
  "use_faster_model": true,
  "reduce_ai_context": true,
  "max_retries": 1
}
```

**Endpoint:** `POST /api/performance_config`

**Request:**
```json
{
  "use_faster_model": false,
  "enable_vector_search": true
}
```

---

#### 8. Health Check
Monitor service health and dependencies.

**Endpoint:** `GET /health`

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-20T10:30:00.000Z",
  "version": "1.0.0",
  "service": "MySQL Backend API",
  "openai_client": "connected",
  "pinecone": "connected",
  "database": "connected",
  "database_name": "mydatabase",
  "performance_config": {
    "vector_search_enabled": false,
    "performance_analysis_enabled": false,
    "query_validation_enabled": false,
    "using_faster_model": true
  }
}
```

## 💡 Usage Examples

### Python Client Example

```python
import requests
import json

# Create session for connection persistence
session = requests.Session()
base_url = "http://localhost:5000/api"

# Connect to database
connection_data = {
    "host": "localhost",
    "user": "myuser",
    "password": "mypassword",
    "database": "ecommerce"
}
response = session.post(f"{base_url}/connect", json=connection_data)
print("Connected:", response.json())

# Get available tables
response = session.get(f"{base_url}/get_tables")
tables = response.json()["tables"]
print("Available tables:", tables)

# Process a table for better query understanding
session.post(f"{base_url}/process_table", json={"table_name": "customers"})

# Ask a natural language question
query_data = {
    "question": "What are the top 5 customers by total purchase amount?",
    "table_name": "customers"
}
response = session.post(f"{base_url}/query", json=query_data)
result = response.json()

print("Generated SQL:", result["generated_query"])
print("AI Summary:", result["ai_summary"])
print("Results:", json.dumps(result["results"], indent=2))

# Disconnect when done
session.post(f"{base_url}/disconnect")
```

### JavaScript/Node.js Example

```javascript
const axios = require('axios');

// Create axios instance with cookie jar for sessions
const client = axios.create({
  baseURL: 'http://localhost:5000/api',
  withCredentials: true
});

async function queryDatabase() {
  try {
    // Connect to database
    await client.post('/connect', {
      host: 'localhost',
      user: 'myuser',
      password: 'mypassword',
      database: 'ecommerce'
    });

    // Execute natural language query
    const response = await client.post('/query', {
      question: 'Show me revenue by month for the last year',
      table_name: 'orders'
    });

    console.log('Generated SQL:', response.data.generated_query);
    console.log('Results:', response.data.results);
    console.log('AI Summary:', response.data.ai_summary);

    // Disconnect
    await client.post('/disconnect');
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
  }
}

queryDatabase();
```

### cURL Examples

```bash
# Connect to database
curl -X POST http://localhost:5000/api/connect \
  -H "Content-Type: application/json" \
  -c cookies.txt \
  -d '{
    "host": "localhost",
    "user": "root",
    "password": "password",
    "database": "testdb"
  }'

# Execute query
curl -X POST http://localhost:5000/api/query \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{
    "question": "How many users registered this week?",
    "table_name": "users"
  }'
```

## 🔧 Performance Optimization

### Configuration Options

1. **Use Faster Model** (`use_faster_model`)
   - `true`: Uses GPT-3.5-turbo (faster, cheaper)
   - `false`: Uses GPT-4 (more accurate, slower)

2. **Reduce AI Context** (`reduce_ai_context`)
   - `true`: Minimal prompts for faster response
   - `false`: Detailed context for better accuracy

3. **Enable Vector Search** (`enable_vector_search`)
   - `true`: Uses Pinecone for semantic understanding
   - `false`: Direct SQL generation (faster)

4. **Enable Query Validation** (`enable_query_validation`)
   - `true`: Validates SQL with EXPLAIN before execution
   - `false`: Skip validation for speed

### Recommended Configurations

**High Performance Mode:**
```json
{
  "use_faster_model": true,
  "reduce_ai_context": true,
  "enable_vector_search": false,
  "enable_query_validation": false,
  "max_retries": 1
}
```

**High Accuracy Mode:**
```json
{
  "use_faster_model": false,
  "reduce_ai_context": false,
  "enable_vector_search": true,
  "enable_query_validation": true,
  "max_retries": 3
}
```

## 🚢 Deployment

### Docker Deployment

Create a `Dockerfile`:
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "app:app"]
```

Build and run:
```bash
docker build -t mysql-ai-service .
docker run -p 5000:5000 --env-file .env mysql-ai-service
```

### Docker Compose

Create `docker-compose.yml`:
```yaml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "5000:5000"
    env_file:
      - .env
    restart: unless-stopped
```

### AWS Deployment

1. **Elastic Beanstalk**
   ```yaml
   # .ebextensions/python.config
   option_settings:
     aws:elasticbeanstalk:container:python:
       WSGIPath: app.py
   ```

2. **Lambda + API Gateway**
   - Use Serverless Framework or SAM
   - Requires connection pooling for MySQL

### Heroku Deployment

```bash
# Create Procfile
echo "web: gunicorn app:app" > Procfile

# Deploy
heroku create your-app-name
heroku config:set OPENAI_API_KEY=your-key
heroku config:set PINECONE_API_KEY=your-key
git push heroku main
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-ai-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mysql-ai-service
  template:
    metadata:
      labels:
        app: mysql-ai-service
    spec:
      containers:
      - name: api
        image: mysql-ai-service:latest
        ports:
        - containerPort: 5000
        env:
        - name: OPENAI_API_KEY
          valueFrom:
            secretKeyRef:
              name: api-secrets
              key: openai-key
```

## 🔒 Security Considerations

1. **API Keys**: Always use environment variables, never commit keys
2. **Database Credentials**: Use connection pooling and encrypted storage
3. **Query Validation**: Service only allows SELECT queries by default
4. **CORS**: Configure allowed origins for your specific use case
5. **Rate Limiting**: Implement rate limiting for production
6. **HTTPS**: Always use HTTPS in production

### Security Best Practices

```python
# Example: Adding rate limiting
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    app,
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)

@app.route('/api/query', methods=['POST'])
@limiter.limit("10 per minute")
def handle_query():
    # ... existing code ...
```

## 🐛 Troubleshooting

### Common Issues

1. **"No active database connection"**
   - Ensure you've called `/api/connect` first
   - Check session/cookie handling in your client

2. **"Failed to generate valid SQL"**
   - Verify table name is correct
   - Try rephrasing your question
   - Check if table has been processed with `/api/process_table`

3. **"Query execution failed"**
   - Check database permissions
   - Verify column names in generated SQL
   - Look for syntax errors in complex queries

4. **Performance issues**
   - Enable performance mode
   - Reduce result set size with specific queries
   - Use connection pooling for high traffic

### Debug Endpoints

```bash
# Check session information
curl http://localhost:5000/api/debug/session -b cookies.txt

# Check service health
curl http://localhost:5000/health
```

## 📊 Monitoring & Logging

### Logging Configuration

```python
# Add to your configuration
import logging
from logging.handlers import RotatingFileHandler

if not app.debug:
    file_handler = RotatingFileHandler('logs/mysql-ai.log', maxBytes=10240, backupCount=10)
    file_handler.setFormatter(logging.Formatter(
        '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
    ))
    file_handler.setLevel(logging.INFO)
    app.logger.addHandler(file_handler)
```

### Metrics to Monitor

- API response times
- Query generation success rate
- Database connection pool status
- OpenAI API usage
- Error rates by endpoint

## 🛣️ Roadmap

- [ ] Support for PostgreSQL and SQLite
- [ ] Built-in rate limiting
- [ ] WebSocket support for real-time queries
- [ ] Query caching layer
- [ ] Multi-language support
- [ ] GraphQL endpoint
- [ ] Built-in authentication system
- [ ] Query history and analytics
- [ ] Admin dashboard
- [ ] Batch query processing

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Setup

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
pytest

# Run linting
flake8 app.py

# Format code
black app.py
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- OpenAI for GPT models
- Pinecone for vector database
- Flask community for the excellent framework
- All contributors and users of this service

## 📞 Support

- **Documentation**: This README
- **Issues**: [GitHub Issues](https://github.com/yourusername/mysql-ai-service/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/mysql-ai-service/discussions)
- **Email**: support@yourservice.com

---

**Built with ❤️ for developers who want to make databases conversational** 