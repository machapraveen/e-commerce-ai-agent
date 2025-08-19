# E-Commerce AI Agent

A sophisticated Flask web application that leverages AI agents to search and compare products across multiple e-commerce platforms simultaneously. Built with LangChain, LangGraph, and Model Context Protocol (MCP) integration for intelligent product discovery.

## Author
**Macha Praveen**

## Overview

This application uses an AI-powered agent to intelligently search for products across major e-commerce platforms including Amazon, Best Buy, eBay, Walmart, Target, Costco, and Newegg. The agent uses web scraping and structured data extraction to provide comprehensive product comparisons in a user-friendly interface.

## Features

- **Multi-Platform Search**: Search across 7 major e-commerce platforms simultaneously
- **AI-Powered Agent**: Uses GPT-4 with ReAct (Reasoning and Acting) framework for intelligent product discovery
- **Structured Responses**: Returns organized product data with URLs, titles, and ratings
- **MCP Integration**: Leverages Model Context Protocol for robust web data extraction
- **Real-time Results**: Asynchronous processing for fast response times
- **Responsive Web Interface**: Bootstrap-powered UI with modern styling

## Architecture

### Core Components

**AI Agent Framework:**
```python
# Agent setup with structured response format
agent = create_react_agent(model, tools, response_format=ProductSearchResponse)

# Structured response model
class ProductSearchResponse(BaseModel):
    platforms: list[PlatformBlock] = Field(..., description='Aggregated list of all results grouped by platform')
```

**MCP Server Integration:**
```python
server_params = StdioServerParameters(
    command='npx',
    args=['@brightdata/mcp'],
    env = {
        'API_TOKEN': os.getenv('API_TOKEN'),
        'BROWSER_AUTH': os.getenv('BROWSER_AUTH'),
        'WEB_UNLOCKER_ZONE': os.getenv('WEB_UNLOCKER_ZONE')
    }
)
```

**Async Agent Execution:**
```python
async def run_agent(query, platforms):
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(read, write) as sess:
            await sess.initialize()
            tools = await load_mcp_tools(sess)
            agent = create_react_agent(model, tools, response_format=ProductSearchResponse)
            result = await agent.ainvoke({
                'messages': [
                    {'role': 'system', 'content': SYSTEM_PROMPT},
                    {'role': 'user', 'content': prompt}
                ]
            })
            return result['structured_response'].model_dump()
```

## Supported Platforms

1. **Amazon** - World's largest online marketplace
2. **Best Buy** - Electronics and technology products
3. **eBay** - Auction and marketplace platform
4. **Walmart** - Retail giant with online presence
5. **Target** - Department store with e-commerce
6. **Costco** - Warehouse club retailer
7. **Newegg** - Computer hardware and electronics specialist

## Installation

### Prerequisites
- Python 3.8+
- Node.js (for MCP server)
- OpenAI API key
- BrightData credentials (for web scraping)

### Dependencies Installation
```bash
pip install flask python-dotenv pydantic langchain-openai langchain-mcp-adapters langgraph mcp
npm install -g @brightdata/mcp
```

### Environment Configuration
Create a `.env` file with the following variables:
```env
OPENAI_API_KEY=your_openai_api_key
API_TOKEN=your_brightdata_api_token
BROWSER_AUTH=your_browser_auth_credentials
WEB_UNLOCKER_ZONE=your_web_unlocker_zone
```

## Usage

### Running the Application
```bash
python app.py
```

The application will start on `http://0.0.0.0:8000`

### Web Interface Usage

1. **Enter Search Query**: Type your product search query (e.g., "best budget gaming laptop")
2. **Select Platforms**: Choose which e-commerce platforms to search
3. **Run Agent**: Click the "Run agent" button to execute the search
4. **View Results**: Browse organized results by platform with direct links to products

### Example Search Flow

**Input:**
- Query: "wireless bluetooth headphones under $100"
- Platforms: Amazon, Best Buy, Target

**Output:**
```json
{
  "platforms": [
    {
      "platform": "Amazon",
      "results": [
        {
          "url": "https://amazon.com/product/...",
          "title": "Sony WH-CH720N Wireless Noise Canceling Headphones",
          "rating": "4.3 stars (12,450 ratings)"
        }
      ]
    },
    {
      "platform": "Best Buy",
      "results": [
        {
          "url": "https://bestbuy.com/site/...",
          "title": "JBL Tune 760NC Wireless Over-Ear Headphones",
          "rating": "4.4/5 (892 reviews)"
        }
      ]
    }
  ]
}
```

## Technology Stack

- **Backend**: Flask (Python web framework)
- **AI/ML**: LangChain, LangGraph, OpenAI GPT-4
- **Web Scraping**: BrightData MCP Server
- **Data Validation**: Pydantic models
- **Frontend**: Bootstrap 5, Jinja2 templates
- **Async Processing**: Python asyncio
- **Environment Management**: python-dotenv

## Key Features Explained

### Intelligent Agent System
The application uses LangGraph's ReAct agent pattern, which combines reasoning and acting to intelligently search for products. The agent first reasons about the search strategy, then acts by using appropriate tools for each platform.

### Structured Data Extraction
All product data is validated using Pydantic models, ensuring consistent structure across different e-commerce platforms:

```python
class Hit(BaseModel):
    url: str = Field(..., description='The URL of the product that was found')
    title: str = Field(..., description='The title of the product that was found')
    rating: str = Field(..., description='The rating of the product (stars, number of ratings given etc.)')
```

### MCP Integration
The Model Context Protocol integration allows the agent to access specialized web scraping tools for each e-commerce platform, providing reliable and structured data extraction.

## Development Notes

- The application uses asynchronous processing to handle multiple platform searches efficiently
- Error handling includes user-friendly flash messages for various failure scenarios
- The responsive design adapts to different screen sizes and devices
- Platform-specific optimizations ensure accurate product data extraction

## Future Enhancements

- Price comparison and historical tracking
- User accounts and saved searches
- Product recommendation system
- Integration with additional e-commerce platforms
- Advanced filtering and sorting options

## License

This project is open-source and available under the MIT License.
