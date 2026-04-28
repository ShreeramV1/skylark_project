# Monday.com Business Intelligence Agent

A conversational AI agent that analyzes work orders and sales pipelines from Monday.com boards, providing founders and executives with real-time business intelligence through natural language queries.

Hosted link : https://cute-jelly-b828f9.netlify.app/

## 🎯 Features

- **Dual-Board Integration**: Analyze Work Orders and Deals pipeline simultaneously
- **Natural Language Queries**: Ask business questions in plain English
- **Conversation Memory**: Maintains context across 3 exchanges for follow-up questions
- **Multi-Sector Analysis**: Compare metrics across Mining, Powerline, Renewables, DSP, Tender, and Railways sectors
- **Real-time Data**: Fresh data fetched on every query with intelligent caching
- **Executive Summaries**: Clean, formatted responses with tables and key insights
- **Graceful Data Handling**: Processes messy real-world data with missing values

## 📊 Supported Queries

```
"How's our pipeline for the energy sector?"
"What's our total billed amount this month?"
"Which deals are most likely to close?"
"Compare work orders vs deal pipeline for renewables"
"Show me high probability deals with their status"
"What's our revenue by sector?"
"Tell me more about mining deals" (follow-up with context)
```

## 🚀 Quick Start

### Prerequisites
- Node.js 18+
- npm or yarn
- Monday.com API token
- Google Gemini API key

### Backend Setup

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/monday-bi-agent.git
cd monday-bi-agent
```

2. **Install dependencies**
```bash
npm install
```

3. **Configure environment variables**
Create a `.env` file:
```env
MONDAY_API_TOKEN=your_monday_api_token
GEMINI_API_KEY=your_gemini_api_key
MONDAY_BOARD_ID=your_work_orders_board_id
MONDAY_DEALS_BOARD_ID=your_deals_board_id
NODE_ENV=development
PORT=3001
```

4. **Start the backend**
```bash
npm start
```

Server runs on `http://localhost:3001`

### Frontend Setup

1. **Navigate to frontend directory**
```bash
cd chatbot-ui
```

2. **Install dependencies**
```bash
npm install
```

3. **Create environment file**
```bash
# .env.local
REACT_APP_API_URL=http://localhost:3001
```

4. **Start development server**
```bash
npm start
```

UI runs on `http://localhost:3000`

## 📦 Deployment

### Backend (Render)

1. Push code to GitHub
2. Go to [render.com](https://render.com)
3. Create new Web Service
4. Connect GitHub repo
5. Set build command: `npm install`
6. Set start command: `npm start`
7. Add environment variables:
   - `MONDAY_API_TOKEN`
   - `GEMINI_API_KEY`
   - `MONDAY_BOARD_ID`
   - `MONDAY_DEALS_BOARD_ID`

### Frontend (Netlify)

1. Build the React app:
```bash
cd chatbot-ui
npm run build
```

2. Deploy to Netlify:
   - Connect GitHub repo
   - Build command: `npm run build`
   - Publish directory: `build`
   - Add env variable: `REACT_APP_API_URL=https://your-render-url.onrender.com`

## 📚 API Endpoints

### POST `/api/query`
Ask a business question.

**Request:**
```json
{
  "message": "Show me the energy sector pipeline"
}
```

**Response:**
```json
{
  "answer": "# Energy Sector Pipeline Overview\n\n...",
  "dataQuality": {
    "totalRecords": 175,
    "completeRecords": 175
  }
}
```

### GET `/api/health`
Check backend status.

**Response:**
```json
{
  "status": "ok",
  "timestamp": "2026-04-28T10:30:00.000Z",
  "workOrdersBoard": "5028090374",
  "dealsBoard": "5028090373",
  "conversationLength": 2,
  "environment": "production"
}
```

### GET `/api/history`
View conversation history.

**Response:**
```json
{
  "status": "ok",
  "conversationLength": 6,
  "history": [
    {
      "role": "user",
      "content": "Show me the energy sector pipeline"
    },
    {
      "role": "model",
      "content": "# Energy Sector Pipeline..."
    }
  ]
}
```

### POST `/api/clear-history`
Clear conversation history.

**Response:**
```json
{
  "status": "ok",
  "message": "Conversation history cleared"
}
```

## 🔐 Security

### Current Implementation
- API keys stored in environment variables
- CORS enabled for frontend communication
- Request validation on all endpoints

### Production Recommendations
- Use AWS Secrets Manager or HashiCorp Vault for key management
- Implement rate limiting (e.g., express-rate-limit)
- Add authentication/authorization layer
- Use HTTPS only (Render/Netlify handle this)
- Add request signing for API calls

## 📊 Data Normalization

### Work Orders Board
Maps Monday.com columns to business fields:
- Deal value (amount excl/incl GST)
- Billing & collection metrics
- Execution status & timeline
- Sector classification

### Deals Board
Maps deal pipeline data:
- Deal value & probability
- Deal stage & status
- Close dates (actual & tentative)
- Sector/service categorization

**Handling Messy Data:**
- Null values treated as 0 or 'N/A'
- Currency parsed from masked strings
- Status values normalized to standard categories
- Missing sectors classified as 'Unclassified'

## 💡 Key Features Explained

### Conversation Memory
- Keeps last 3 exchanges (6 messages)
- Auto-trims old messages when exceeded
- Provides context to LLM for follow-up questions
- Prevents token bloat & response lag

### Dual-Dataset Analysis
- Simultaneously analyzes Work Orders + Deals
- Generates combined data analysis
- Enables cross-board insights (pipeline-to-execution conversion)
- Highlights sector-wise trends

### Executive Summaries
- Lead with headline metrics
- Clean markdown formatting
- Tables for comparisons
- Data quality flagging (when >50% records incomplete)

## 🐛 Troubleshooting

### 500 Error on Query
1. Check Render logs: `https://dashboard.render.com`
2. Verify environment variables are set
3. Test Monday.com API token:
```bash
curl -X POST https://api.monday.com/v2 \
  -H "Authorization: YOUR_TOKEN" \
  -d '{"query": "{ me { name } }"}'
```
4. Test Gemini API key in [Google AI Studio](https://aistudio.google.com)

### CORS Error
- Backend CORS is enabled in `server.js`
- Verify frontend URL in Netlify environment variables
- Check browser console for actual error

### No Data Returned
- Verify board IDs are correct
- Check if boards have items
- Test data fetch: `curl http://localhost:3001/api/health`

### Follow-up Questions Losing Context
- History should auto-maintain (last 3 exchanges)
- Check conversation history: `/api/history`
- Clear if needed: `POST /api/clear-history`
