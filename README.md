# MySQL AI Assistant

An intelligent MySQL database assistant that helps you query and analyze your data using natural language. Now available with both Flask (original) and React (modern) frontends!

## 🚀 New React Frontend Available!

We've successfully migrated the HTML interface to a modern React + Vite application. Choose your preferred frontend:

### React Frontend (Recommended)
- **Location**: `frontend/` directory
- **Technology**: React 18 + Vite
- **Features**: Modern UI, better performance, component-based architecture
- **URL**: http://localhost:5173

### Flask Frontend (Original)
- **Location**: `templates/` directory  
- **Technology**: Flask templates with vanilla JavaScript
- **Features**: Server-side rendering, traditional web app
- **URL**: http://localhost:5000

## Features

- **Natural Language Queries**: Ask questions about your database in plain English
- **Smart SQL Generation**: AI-powered SQL query generation and optimization
- **Data Visualization**: Intelligent formatting and grouping of query results
- **Export Capabilities**: Export results to CSV and JSON formats
- **Performance Optimization**: Configurable AI model settings for speed vs accuracy
- **Database Schema Analysis**: Automatic table discovery and relationship mapping
- **Real-time Chat Interface**: Interactive conversation with your database
- **Connection Management**: Secure database connection handling

## Quick Start

### Option 1: React Frontend (Recommended)

1. **Start the Flask Backend**:
```bash
python app.py
```

2. **Start the React Frontend**:
```bash
cd frontend
npm install
npm run dev
```

3. **Access the Application**:
   - React UI: http://localhost:5173
   - Backend API: http://localhost:5000

### Option 2: Flask Frontend (Original)

1. **Start the Flask Application**:
```bash
python app.py
```

2. **Access the Application**:
   - Web Interface: http://localhost:5000

## Installation

### Prerequisites
- Python 3.8+
- Node.js 16+ (for React frontend)
- MySQL Server
- OpenAI API key

### Backend Setup

1. **Clone the repository**:
```bash
git clone <repository-url>
cd mysql-chatbot
```

2. **Create virtual environment**:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**:
```bash
pip install -r requirements.txt
```

4. **Set up environment variables**:
```bash
# Create .env file
OPENAI_API_KEY=your_openai_api_key_here
```

### Frontend Setup (React)

1. **Navigate to frontend directory**:
```bash
cd frontend
```

2. **Install dependencies**:
```bash
npm install
```

3. **Start development server**:
```bash
npm run dev
```

## Project Structure

```
mysql-chatbot/
├── frontend/                 # React frontend (NEW)
│   ├── src/
│   │   ├── components/      # React components
│   │   ├── App.jsx         # Main app component
│   │   └── index.css       # Global styles
│   ├── package.json
│   └── vite.config.js
├── templates/               # Flask templates (Original)
│   ├── index.html          # Main interface
│   └── flowchart.html      # Database visualization
├── src/                    # Backend modules
│   ├── services/           # Business logic
│   ├── contexts/           # Context managers
│   └── components/         # Utility components
├── app.py                  # Flask application
├── requirements.txt        # Python dependencies
└── README.md              # This file
```

## API Endpoints

The Flask backend provides these REST API endpoints:

- `POST /connect` - Connect to MySQL database
- `POST /disconnect` - Disconnect from database
- `GET /get_tables` - Retrieve database tables
- `POST /chat` - Send natural language queries
- `GET /api/performance_config` - Get performance settings
- `POST /api/performance_config` - Update performance settings

## Configuration

### Performance Settings
- **Use Faster Model**: Trade accuracy for speed
- **Reduce AI Context**: Limit context for faster responses
- **Enable Vector Search**: Use semantic search for better results
- **Enable Performance Analysis**: Get query performance insights

### Database Connection
- **Host**: MySQL server hostname
- **Port**: MySQL server port (default: 3306)
- **Username**: Database username
- **Password**: Database password
- **Database**: Target database name

## Migration Notes

### HTML to React Migration Completed ✅

The original HTML interface has been successfully migrated to React with the following improvements:

#### ✨ New Features
- **Component-based architecture** for better maintainability
- **Modern React hooks** for state management
- **Vite build system** for fast development and optimized builds
- **Improved error handling** and user feedback
- **Better responsive design** for mobile devices
- **Enhanced accessibility** features

#### 🔄 Preserved Features
- **All original functionality** maintained
- **Same API integration** with Flask backend
- **Identical styling** and user experience
- **Export capabilities** (CSV/JSON)
- **Performance settings** configuration
- **Database connection management**

#### 🚀 Technical Improvements
- **Faster load times** with Vite
- **Better development experience** with hot reload
- **Modular code structure** for easier maintenance
- **Modern JavaScript** with ES6+ features
- **Optimized bundle size** for production

## Development

### React Frontend Development
```bash
cd frontend
npm run dev          # Start development server
npm run build        # Build for production
npm run preview      # Preview production build
npm run lint         # Run ESLint
```

### Flask Backend Development
```bash
python app.py        # Start Flask development server
```

## Production Deployment

### React Frontend
```bash
cd frontend
npm run build        # Creates dist/ directory
# Serve dist/ directory with any static file server
```

### Flask Backend
```bash
# Use production WSGI server like Gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test both frontends
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues and questions:
1. Check the documentation in each frontend's README
2. Review the API endpoints documentation
3. Create an issue on GitHub

---

**Choose Your Frontend**: Both interfaces provide the same powerful features - pick the one that fits your development preferences!
