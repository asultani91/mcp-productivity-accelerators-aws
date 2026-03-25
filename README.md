
# 🔧 Everyday Productivity Accelerators: Building GenAI Tools with Model Context Protocol (MCP)

A comprehensive 2-hour hands-on workshop demonstrating how to build AI-powered productivity tools using **Model Context Protocol (MCP)**, **Amazon Bedrock AgentCore Runtime**, and **Strands Agents**. Transform productivity by connecting AI models to real-world tools through standardized protocols.

> **Workshop Code:** AIM306
> **Duration:** 2 hours
> **Level:** 200 (Intermediate)
> **Target Audience:** Solution Architects, Software Designers, Developers

---

## 🔌 What is MCP?

**Model Context Protocol (MCP)** is an open protocol that standardizes how applications provide context to Large Language Models (LLMs).

### The USB-C Analogy
> "MCP is like USB-C for AI applications. Just as USB-C provides a standardized way to connect your devices to various peripherals and accessories, MCP provides a standardized way to connect AI models to different data sources and tools."

### Core Benefits

| Benefit | Description |
|---------|-------------|
| **Standardization** | One protocol for all tools - calculator, calendar, weather use same interface |
| **Composability** | Combine multiple tools seamlessly (calendar + weather for meeting planning) |
| **Interoperability** | Works across AI models (Claude, GPT, Nova all use same tools) |
| **Security** | Controlled access patterns - tools only access what they need |

---

## 🎯 What I Built

Complete set of AI-powered productivity tools integrated into a pre-built React application:

| Tool | Capabilities | Key Features |
|------|-------------|--------------|
| **Smart Calculator** | Advanced math operations | Natural language understanding, complex calculations |
| **Calendar Assistant** | Intelligent schedule management | Conflict detection, optimal meeting time suggestions |
| **Weather Advisor** | Smart weather forecasting | Activity recommendations based on conditions |
| **Custom MCP Server** | Personalized productivity tool | Temperature converter with Fahrenheit/Celsius conversion |
| **Remote Deployment** | Cloud-hosted MCP server | AWS AgentCore Runtime with IAM authentication |

---

## 💡 Workshop Philosophy

**"UI is Ready, Focus on the Intelligence"**

The workshop provides a complete React frontend so I could focus on building AI-powered backend tools that make a real difference in productivity.

### Why This Approach?
- No time wasted on UI development
- Immediate testing with natural language
- Focus on MCP server logic and AI integration
- Production-ready application structure

---

## 🏗️ Architecture

### **MCP Communication Flow**
```
User Query → AI Agent (Nova Pro/Claude)
    ↓
MCP Protocol (tools/call)
    ↓
Calculator Server → add(25, 17)
    ↓
Result: 42
    ↓
Agent Response: "25 + 17 equals 42"
```

### **Multi-Tool Workflow**
```
User: "Schedule team lunch outside next week"
    ↓
AI Agent Analysis
    ├→ Weather Server (7-day forecast)
    ├→ Calendar Server (find free slots)
    ├→ Calendar Server (check team schedules)
    ↓
Decision Engine (optimal day + time)
    ↓
Calendar Server (create event)
    ↓
Response: "Team lunch scheduled Thursday 12:30pm - sunny & 75°F!"
```

### **AgentCore Deployment Architecture**
```
Local Development:
Workshop App → stdio → Local MCP Servers

Cloud Deployment:
Workshop App → HTTP (IAM Auth) → AgentCore Runtime
    ↓
Amazon ECR (Docker Images)
    ↓
CloudWatch (Monitoring)
```

---

## 🧪 Productivity Tools Built

### **1. Calculator Server**

**Features:**
- Natural language math queries
- Basic operations (add, subtract, multiply, divide)
- Advanced functions (percentage, square root, power)
- Context awareness and error handling

**Implementation Example:**
```python
from mcp.server import FastMCP
import math

mcp = FastMCP("Calculator Server")

@mcp.tool(description="Add two numbers together")
def add(x: float, y: float) -> float:
    """Add two numbers and return the result."""
    return x + y

@mcp.tool(description="Divide first number by second number")
def divide(x: float, y: float) -> float:
    """Divide x by y and return the result."""
    if y == 0:
        raise ValueError("Cannot divide by zero")
    return x / y

@mcp.tool(description="Calculate percentage of a number")
def percentage(number: float, percent: float) -> float:
    """Calculate what percent of number equals."""
    return (number * percent) / 100

if __name__ == "__main__":
    print("🔢 Starting Calculator MCP Server...")
    mcp.run(transport="stdio")
```

**Natural Language Examples:**
- "What is 25 + 17?" → Uses `add()` tool
- "Calculate 15% of 200" → Uses `percentage()` tool
- "What's the square root of 64?" → Uses `square_root()` tool

---

### **2. Calendar Integration**

**Features:**
- Smart scheduling with conflict detection
- Natural language event creation
- Availability checking
- Alternative time suggestions

**Key Functions:**

| Function | Purpose | Intelligence |
|----------|---------|--------------|
| `create_event` | Schedule meetings/appointments | Conflict detection, optimal time suggestions |
| `list_events` | View calendar for specific dates | Smart filtering, availability analysis |
| `check_availability` | Find free time slots | Multi-day search, duration-aware |
| `delete_event` | Remove scheduled events | Cascade handling, notification support |

**Natural Language Examples:**
- "Schedule a team meeting tomorrow at 2pm"
- "Do I have anything on Friday afternoon?"
- "Find me 30 minutes free time this week"

---

### **3. Weather Intelligence**

**Features:**
- Real-time weather using National Weather Service API
- Multi-day forecasts with detailed periods
- Weather alerts for US states
- Current observation data

**Key Functions:**

| Function | Description | Data Source |
|----------|-------------|-------------|
| `get_alerts` | Weather alerts for US states | NWS active alerts API |
| `get_forecast` | Multi-day predictions | NWS forecast API |
| `get_current_weather` | Current observation data | NWS observation stations |

**Natural Language Examples:**
- "Get current weather for Seattle"
- "Show me the forecast for San Francisco"
- "Are there any weather alerts in California?"

**Geographic Coverage:** US locations only (National Weather Service API)

---

### **4. Custom MCP Server - Temperature Converter**

**What I Built:**
A temperature conversion tool with Fahrenheit/Celsius conversion capabilities.

**Implementation:**
```python
from mcp.server import FastMCP
from typing import Dict
from datetime import datetime

mcp = FastMCP("Temperature Conversion Server")

@mcp.tool(description="Convert Fahrenheit to Celsius")
def fahrenheit_to_celsius(temp_f: str) -> Dict:
    """Convert temperature from Fahrenheit to Celsius."""
    try:
        fahrenheit = float(temp_f.strip())
        celsius = (fahrenheit - 32) * 5/9
        
        return {
            "success": True,
            "fahrenheit": fahrenheit,
            "celsius": round(celsius, 2),
            "message": f"{fahrenheit}°F = {round(celsius, 2)}°C",
            "timestamp": datetime.now().isoformat()
        }
    except ValueError:
        return {
            "success": False,
            "error": "Invalid temperature format. Please provide a number.",
            "timestamp": datetime.now().isoformat()
        }

@mcp.tool(description="Convert Celsius to Fahrenheit")
def celsius_to_fahrenheit(temp_c: str) -> Dict:
    """Convert temperature from Celsius to Fahrenheit."""
    try:
        celsius = float(temp_c.strip())
        fahrenheit = (celsius * 9/5) + 32
        
        return {
            "success": True,
            "celsius": celsius,
            "fahrenheit": round(fahrenheit, 2),
            "message": f"{celsius}°C = {round(fahrenheit, 2)}°F"
        }
    except ValueError:
        return {"success": False, "error": "Invalid temperature format."}

if __name__ == "__main__":
    print(f"🚀 Starting {mcp.name}...")
    mcp.run(transport="stdio")
```

**Test Queries:**
- "What's today's temperature in Celsius?"
- "What is 101 F to C?"
- "What is the boiling point of water in C?"

---

## ☁️ AgentCore Deployment

### **Why Amazon Bedrock AgentCore Runtime?**

Traditional deployments require managing servers, scaling, authentication, and monitoring. **AgentCore Runtime handles all of this automatically.**

### **Key Features**

| Feature | Benefit |
|---------|---------|
| **Zero Infrastructure Management** | No servers to manage - fully managed service |
| **Automatic Scaling** | Stateless design allows horizontal scaling |
| **AWS IAM Authentication** | Enterprise-grade security with SigV4 signing |
| **Built-in Monitoring** | CloudWatch integration for logs and metrics |
| **Session Isolation** | Each client gets unique session ID |

### **Deployment Process**

**Step 1: Install AgentCore Toolkit**
```bash
pip install bedrock-agentcore-starter-toolkit
```

**Step 2: Deploy to AgentCore**
```bash
cd /workshop/ui/backend/mcp_servers/agentcore
python3 deploy_to_agentcore.py
```

**What Happens:**
1. Creates Docker container with your code
2. Pushes to Amazon ECR
3. Deploys to AgentCore Runtime
4. Configures auto-scaling and monitoring
5. Stores Agent ARN in Parameter Store

**Expected Output:**
```
🌍 Deploying to region: us-west-2

⚙️  Configuring AgentCore Runtime...
✓ Configuration completed

🚀 Launching to AgentCore Runtime...
This may take 3-5 minutes...

✅ Deployment Complete!
Agent ARN: arn:aws:bedrock-agentcore:us-west-2:123456789012:runtime/abc123
Agent ID: abc123
✓ Agent ARN stored in Parameter Store
```

**Step 3: Auto-Configure Remote Connection**
```bash
python3 update_config.py
```

This automatically:
- Retrieves Agent ARN from Parameter Store
- Builds AgentCore URL
- Updates `mcp.json` with correct configuration
- Enables the remote server

---

## 🛠️ Technologies Used

### **AWS Services**

| Service | Purpose |
|---------|---------|
| **Amazon Bedrock** | Foundation model hosting (Claude, Nova) |
| **Amazon Bedrock AgentCore Runtime** | Managed MCP server hosting |
| **Amazon ECR** | Docker container registry |
| **AWS IAM** | Authentication and access control |
| **Amazon CloudWatch** | Logging and monitoring |
| **AWS Parameter Store** | Secure configuration storage |
| **Amazon EC2** | Workshop environment hosting |
| **CloudFront** | Secure web access to workshop |

### **Frameworks & Libraries**

| Technology | Purpose |
|------------|---------|
| **FastMCP** | Simplified MCP server development |
| **Strands Agents** | Multi-agent orchestration framework |
| **FastAPI** | Backend web framework |
| **React** | Frontend chat interface |
| **Python 3.11+** | Primary programming language |
| **Node.js 18+** | Frontend build system |
| **OpenTelemetry** | Distributed tracing |

### **AI Models**

| Model | Provider | Use Case |
|-------|----------|----------|
| **Claude 3.7 Sonnet** | Anthropic | Advanced reasoning and conversation |
| **Claude 4.5 Sonnet** | Anthropic | Enhanced capabilities |
| **Amazon Nova Pro** | AWS | Multimodal AI tasks |
| **Amazon Nova Premier** | AWS | High-performance workloads |

### **MCP Protocol & Tools**

- **Model Context Protocol (MCP)** - Open standard for AI-tool communication
- **stdio Transport** - Local MCP server communication
- **HTTP Transport** - Remote MCP server communication
- **JSON-RPC 2.0** - Protocol message format
- **JSON Schema** - Parameter validation

---

## 🎓 Key Learning Outcomes

By completing this workshop, I gained expertise in:

### **MCP Protocol Mastery**
✅ Understanding MCP architecture and communication flow
✅ Tool discovery and registration patterns
✅ Request-response message structures
✅ Transport protocol selection (stdio, HTTP, WebSocket)
✅ Error handling and validation

### **MCP Server Development**
✅ Building servers with FastMCP framework
✅ Implementing tool functions with proper type hints
✅ Managing data persistence and state
✅ Integrating external APIs (National Weather Service)
✅ Handling natural language queries

### **Multi-Tool Orchestration**
✅ Combining multiple MCP servers for complex tasks
✅ AI agent decision-making and tool selection
✅ Context management across tool interactions
✅ Workflow design for sophisticated use cases

### **Production Deployment**
✅ Deploying MCP servers to AWS AgentCore Runtime
✅ Configuring IAM authentication with SigV4
✅ Managing remote vs local MCP server configurations
✅ Monitoring with CloudWatch logs
✅ Updating deployed servers with zero downtime

### **Best Practices**
✅ Clear function descriptions for AI understanding
✅ Comprehensive error handling
✅ Proper type hints and validation
✅ User-friendly response formatting
✅ Security considerations (input sanitization, rate limiting)

---

## 📸 Screenshots & Features

The project includes detailed screenshots of:

### **Workshop Interface**
1. **Workshop Studio Event Dashboard** - Event information and access credentials
2. **VS Code Login Screen** - Secure access to cloud IDE
3. **VS Code IDE Interface** - Complete development environment
4. **CloudFormation Stack Outputs** - Workshop resource URLs

### **Main Application Interface (Three Panels)**

**Left Panel - Configuration:**
- AI Models dropdown (Claude 3.7 Sonnet, Nova Pro)
- MCP Servers section with toggles:
  - ✅ Calculator (enabled)
  - ✅ Weather (enabled)
  - ✅ Calendar (enabled)
  - ⚪ My Custom Server (configurable)
  - ⚪ Strand Agent (optional)

**Center Panel - Chat Interface:**
- AI Assistant header with model selection
- Welcome message with feature cards
- Message input field with streaming support
- Token usage tracking

**Right Panel - Server Logs:**
- Real-time MCP server monitoring
- Log entries showing server status
- Filter dropdowns for SERVER and LEVEL
- Auto-scroll toggle
- "Live monitoring active" indicator

### **Chat Interaction Example**
- User query: "Calculate the compound interest: $10,000 invested at 5.5% annual rate for 3 years"
- Assistant response showing:
  - Formula explanation: A = P(1 + r)^t
  - Step-by-step calculation breakdown
  - Use of `multiply` tool (shown in logs)
  - Final result: $11,742.41
  - Compound interest earned: $1,742.41
- Token usage: 22+230 (252 tokens)

All screenshots are stored in the `/images` folder.

---

## 🚀 Getting Started

### **Prerequisites**
- AWS Account with Bedrock access
- Python 3.11+
- Node.js 18+
- Docker (for AgentCore deployment)

### **Local Setup**

1. **Clone the repository**

2. **Install backend dependencies:**
```bash
cd ui/backend
pip install -r requirements.txt
```

3. **Install frontend dependencies:**
```bash
cd ui/frontend
npm install
```

4. **Configure AWS credentials:**
```bash
aws configure
# Enter your AWS Access Key ID
# Enter your AWS Secret Access Key
# Default region: us-west-2
```

5. **Start the application:**
```bash
# Terminal 1 - Backend
cd ui/backend
python main.py

# Terminal 2 - Frontend
cd ui/frontend
npm start
```

6. **Access the application:**
- Frontend: `http://localhost:3000`
- Backend API: `http://localhost:8000`
- API Docs: `http://localhost:8000/docs`

---

## 🌍 Real-World Applications

The MCP patterns learned in this project apply to:

### **Business Applications**

| Industry | AI-Powered Solutions | Expected ROI |
|----------|---------------------|--------------|
| **Customer Service** | Chatbots with CRM + ticketing | 40% reduction in response time |
| **Sales & Marketing** | Lead qualification + automation | 25% increase in conversions |
| **Operations** | Workflow automation | 30% efficiency improvement |
| **Healthcare** | Patient scheduling + compliance | 50% reduction in admin overhead |

---

## 📚 Additional Resources

### **Official Documentation**
- [Model Context Protocol Documentation](https://modelcontextprotocol.io/)
- [Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/)
- [Strands Agents](https://strandsagents.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

### **AWS MCP Servers**
- [40+ Production-Ready MCP Servers from AWS](https://github.com/aws-samples/mcp-servers)

---

## 🧹 Cleanup

### **Stop Workshop Services**
```bash
cd /workshop/ui/
chmod +x stop.sh
./stop.sh
```

### **Clean Up AgentCore Runtime**
```bash
cd /workshop/ui/backend/mcp_servers/agentcore
python3 -c "
from bedrock_agentcore_starter_toolkit.operations.runtime import destroy_bedrock_agentcore
from pathlib import Path
import boto3

# Delete Parameter Store entry
ssm = boto3.client('ssm')
ssm.delete_parameter(Name='/workshop/text_utils_mcp/agent_arn')

# Destroy AgentCore resources
destroy_bedrock_agentcore(
    config_path=Path('.bedrock_agentcore.yaml'),
    agent_name='text_utils_mcp',
    delete_ecr_repo=True
)
"
```

**Expected Cost:** $0.50 - $2.00 USD for 2-hour workshop

---

## 🏁 Summary

### **Workshop Achievements**

| Component | What I Built | Impact |
|-----------|-------------|--------|
| **Calculator Server** | Natural language math with error handling | Foundation for any computational tool |
| **Calendar Integration** | Smart scheduling with conflict detection | Real-world productivity improvement |
| **Weather Intelligence** | Forecasting with activity recommendations | External API integration pattern |
| **Custom MCP Server** | Temperature converter from scratch | Transferable skill for any domain |
| **AgentCore Deployment** | Cloud-hosted MCP server | Production-ready deployment |

### **Key Principles**

✅ **Start Small** - Begin with simple tools, add complexity gradually
✅ **Solve Real Problems** - Focus on genuine user value
✅ **Embrace Standards** - Use MCP for better long-term success
✅ **Think Systems** - Design for scalability from day one

---

## 📄 License

MIT.

---

**Workshop Complete!** You're now equipped to build the next generation of AI-powered productivity tools using the Model Context Protocol. The future is what you make it! 🚀
