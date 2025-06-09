# Contributing to MySQL AI Query Service

Thank you for your interest in contributing to MySQL AI Query Service! This document provides guidelines and instructions for contributing to the project.

## 🤝 Code of Conduct

By participating in this project, you agree to abide by our Code of Conduct:
- Be respectful and inclusive
- Welcome newcomers and help them get started
- Focus on constructive criticism
- Respect differing viewpoints and experiences

## 🚀 Getting Started

1. **Fork the Repository**
   ```bash
   # Fork via GitHub UI, then:
   git clone https://github.com/YOUR_USERNAME/mysql-ai-service.git
   cd mysql-ai-service
   ```

2. **Set Up Development Environment**
   ```bash
   # Create virtual environment
   python -m venv venv
   
   # Activate it
   # Windows:
   venv\Scripts\activate
   # macOS/Linux:
   source venv/bin/activate
   
   # Install dependencies
   pip install -r requirements.txt
   pip install -r requirements-dev.txt
   ```

3. **Create a Branch**
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/your-bug-fix
   ```

## 📝 Development Guidelines

### Code Style

- Follow PEP 8 for Python code
- Use meaningful variable and function names
- Add docstrings to all functions and classes
- Keep functions focused and small

### Testing

- Write tests for new features
- Ensure all tests pass before submitting PR
- Aim for high test coverage

```bash
# Run tests
pytest

# Run tests with coverage
pytest --cov=app --cov-report=html
```

### Linting

```bash
# Run flake8
flake8 app.py

# Run black formatter
black app.py

# Run type checking
mypy app.py
```

## 🔄 Pull Request Process

1. **Before Submitting**
   - Update documentation if needed
   - Add tests for new functionality
   - Ensure all tests pass
   - Update README.md if adding new features

2. **PR Title Format**
   ```
   feat: Add new feature
   fix: Fix bug in query generation
   docs: Update API documentation
   perf: Improve query performance
   refactor: Refactor connection manager
   ```

3. **PR Description Template**
   ```markdown
   ## Description
   Brief description of changes
   
   ## Type of Change
   - [ ] Bug fix
   - [ ] New feature
   - [ ] Breaking change
   - [ ] Documentation update
   
   ## Testing
   - [ ] Tests pass locally
   - [ ] Added new tests
   
   ## Checklist
   - [ ] Code follows style guidelines
   - [ ] Self-review completed
   - [ ] Documentation updated
   ```

## 🏗️ Project Structure

```
mysql-ai-service/
├── app.py                 # Main application file
├── requirements.txt       # Production dependencies
├── requirements-dev.txt   # Development dependencies
├── tests/                # Test files
│   ├── test_api.py      # API endpoint tests
│   ├── test_query.py    # Query generation tests
│   └── test_utils.py    # Utility function tests
├── docs/                 # Documentation
│   ├── api.md           # API documentation
│   └── deployment.md    # Deployment guides
└── examples/            # Usage examples
    ├── python/          # Python examples
    ├── javascript/      # JavaScript examples
    └── curl/            # cURL examples
```

## 🧪 Testing Guidelines

### Unit Tests
```python
# Example test structure
def test_sql_generation():
    """Test SQL query generation from natural language"""
    processor = MySQLSchemaProcessor()
    query = processor.generate_sql_query(
        "Show all users", 
        "users"
    )
    assert query.upper().startswith("SELECT")
```

### Integration Tests
```python
# Test full API flow
def test_query_endpoint(client):
    """Test the query endpoint"""
    response = client.post('/api/query', json={
        'question': 'Show all users',
        'table_name': 'users'
    })
    assert response.status_code == 200
    assert 'generated_query' in response.json
```

## 📚 Adding New Features

### 1. Adding a New Endpoint

```python
@app.route('/api/new_endpoint', methods=['POST'])
def new_endpoint():
    """
    New endpoint description
    
    Returns:
        JSON response with...
    """
    try:
        # Implementation
        return jsonify({'success': True})
    except Exception as e:
        logger.error(f"Error in new_endpoint: {str(e)}")
        return jsonify({'error': str(e)}), 500
```

### 2. Adding New Query Features

1. Update `generate_sql_query()` method
2. Add tests for new functionality
3. Update API documentation
4. Add examples in README

### 3. Adding Database Support

To add support for PostgreSQL/SQLite:
1. Create new connection adapter
2. Update schema retrieval methods
3. Adjust SQL generation for dialect differences
4. Add dialect-specific tests

## 🐛 Reporting Issues

### Bug Reports

Include:
- Python version
- OS version
- Error messages
- Steps to reproduce
- Expected vs actual behavior

### Feature Requests

Include:
- Use case description
- Proposed solution
- Alternative solutions considered
- Additional context

## 💡 Areas for Contribution

### High Priority
- [ ] PostgreSQL support
- [ ] Rate limiting implementation
- [ ] Query caching layer
- [ ] WebSocket support

### Good First Issues
- [ ] Add more examples
- [ ] Improve error messages
- [ ] Add input validation
- [ ] Enhance documentation

### Advanced
- [ ] Multi-database joins
- [ ] Query optimization engine
- [ ] Custom authentication
- [ ] GraphQL endpoint

## 📖 Documentation

### Updating Documentation

1. API changes: Update both README.md and inline docstrings
2. New features: Add examples and use cases
3. Configuration: Update .env.example

### Documentation Style

- Use clear, concise language
- Include code examples
- Explain the "why" not just the "how"
- Keep it up to date

## 🎯 Release Process

1. Version numbering follows SemVer (MAJOR.MINOR.PATCH)
2. Update CHANGELOG.md
3. Tag releases in git
4. Update documentation

## 💬 Getting Help

- Check existing issues and PRs
- Join our Discord/Slack community
- Ask questions in GitHub Discussions
- Review the documentation

## 🙏 Recognition

Contributors will be:
- Added to CONTRIBUTORS.md
- Mentioned in release notes
- Given credit in documentation

Thank you for contributing to MySQL AI Query Service! 