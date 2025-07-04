# ArchiveNet

A Model Context Protocol (MCP) server that provides context insertion and search functionality. This server allows AI assistants to save and retrieve personal and professional data through configurable API endpoints.

## Features

- **Insert Context**: Save personal, professional, or general information with metadata
- **Search Context**: Find previously stored context using queries with filtering
- **Multi-LLM Support**: Works with Claude Desktop and Cursor IDE
- **Configurable Endpoints**: Use your own API endpoints via environment variables
- **Environment Editor**: Interactive tool to configure API endpoints
- **Type Safety**: Full TypeScript implementation with Zod validation
- **Error Handling**: Comprehensive error handling and validation
- **Metadata Support**: Rich metadata including context, tags, timestamps, and client info
- **Bearer Token Authentication**: Optional Bearer token authentication

## Quick Setup

### Automated Setup (Recommended)

Use the unified setup script to automatically configure your preferred LLM:

```bash
# 1. Install dependencies
npm install

# 2. Configure environment (interactive)
npm run edit-env --interactive

# 3. Run automated setup for your LLM
npm run setup claude    # For Claude Desktop
npm run setup cursor    # For Cursor IDE
```

### Manual Setup

1. **Install Dependencies**
   ```bash
   npm install
   ```

2. **Configure Environment**
   
   **Option A: Interactive Configuration (Recommended)**
   ```bash
   npm run edit-env --interactive
   ```
   
   **Option B: Manual Configuration**
   ```bash
   cp .env.example .env
   # Edit .env with your API endpoints
   ```
   
   **Option C: Direct Updates**
   ```bash
   npm run edit-env BASE_API_URL=https://your-api.com
   npm run edit-env TOKEN=your-bearer-token
   ```

3. **Build the Server**
   ```bash
   npm run build
   ```

4. **Configure Your LLM**

   #### For Claude Desktop
   
   Add to your Claude Desktop config file:
   
   **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
   **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
   **Linux**: `~/.config/Claude/claude_desktop_config.json`
   
   ```json
   {
     "mcpServers": {
       "archivenet": {
         "command": "node",
         "args": ["/absolute/path/to/your/project/dist/index.js"],
         "env": {
           "BASE_API_URL": "https://your-api.com",
           "TOKEN": "your-bearer-token",
           "API_TIMEOUT": "30000"
         }
       }
     }
   }
   ```

   #### For Cursor IDE
   
   Add to your Cursor MCP config file:
   
   **All Platforms**: `~/.cursor/mcp.json`
   
   ```json
   {
     "mcpServers": {
       "archivenet": {
         "command": "node",
         "args": ["/absolute/path/to/your/project/dist/index.js"],
         "env": {
           "BASE_API_URL": "https://your-api.com",
           "TOKEN": "your-bearer-token",
           "API_TIMEOUT": "30000"
         }
       }
     }
   }
   ```

5. **Restart Your LLM**

## Environment Configuration

### Using the Environment Editor

The `edit-env` script provides multiple ways to configure your API endpoints:

#### Interactive Mode (Recommended)
```bash
npm run edit-env --interactive
```
This will guide you through setting up all required and optional environment variables.

#### Direct Updates
```bash
# Set individual variables
npm run edit-env BASE_API_URL=https://api.example.com
npm run edit-env TOKEN=your-bearer-token
npm run edit-env API_TIMEOUT=60000

# Set multiple variables at once
npm run edit-env BASE_API_URL=https://api.example.com TOKEN=your-bearer-token
```

#### Show Current Configuration
```bash
npm run edit-env --show
```

#### Reset to Defaults
```bash
npm run edit-env --reset
```

#### Help
```bash
npm run edit-env --help
```

### Environment Variables

- **BASE_API_URL** (required): Base API URL for ArchiveNET (e.g., https://api.archivenet.com)
- **TOKEN** (optional): Bearer token for authentication
- **API_TIMEOUT** (optional): Request timeout in milliseconds (default: 30000)

## Setup Script Usage

The unified setup script supports both Claude and Cursor:

```bash
# Setup for Claude Desktop
scripts/setup-mcp.js claude

# Setup for Cursor IDE
scripts/setup-mcp.js cursor

# Show help
scripts/setup-mcp.js --help
```

### Using with npm/npx

```bash
# Install globally
npm install -g archivenet

# Configure environment
edit-env --interactive

# Run setup
setup-mcp claude

# Or use without installing
npx archivenet
npx setup-mcp claude
npx edit-env --interactive
```

## API Endpoint Requirements

Your API endpoints should implement the following interfaces:

### Insert Endpoint (POST {BASE_API_URL}/memories/insert)
**Request Headers:**
```
Content-Type: application/json
Authorization: Bearer your-token-here
```

**Request Body:**
```json
{
  "content": "User's favorite color is blue",
  "metadata": {
    "context": "preference setting",
    "tags": ["preference", "color"],
    "timestamp": "2025-06-06T14:30:00Z",
    "client": "cursor"
  }
}
```

**Response:**
```json
{
  "success": true,
  "id": "unique-id",
  "message": "Context saved successfully"
}
```

### Search Endpoint (POST {BASE_API_URL}/memories/search)
**Request Headers:**
```
Content-Type: application/json
Authorization: Bearer your-token-here
```

**Request Body:**
```json
{
  "query": "favorite color preference",
  "k": 5,
  "filters": {}
}
```

**Response:**
```json
{
  "success": true,
  "message": "Found 1 relevant memories",
  "data": [
    {
      "id": 0,
      "content": "User's favorite color is blue",
      "metadata": {
        "context": "preference setting",
        "tags": ["preference", "color"],
        "timestamp": "2025-06-06T14:30:00Z",
        "client": "cursor"
      },
      "distance": 0.3285933909986025
    }
  ]
}
```

## Tools Available

### insert_context
Saves personal or professional context data for future reference.

**Parameters:**
- `content` (required): The content to store
- `metadata` (optional): Object containing:
  - `context`: Context description (e.g., "preference setting")
  - `tags`: Array of tags for organization
  - `timestamp`: ISO timestamp (auto-generated if not provided)
  - `client`: Client identifier (defaults to "mcp-server")

### search_context
Searches through previously stored context data.

**Parameters:**
- `query` (required): Search query
- `k`: Number of results to return (default: 5)
- `filters` (required): Object containing:
  - `tags`: Array of tags to filter by (optional)

## Usage with AI Assistants

This MCP server automatically detects when users share personal or professional information and can save it using the `insert_context` tool. It can then retrieve relevant information using the `search_context` tool when needed.

The server is designed to work seamlessly with AI assistants that support the Model Context Protocol.

## Development

- `npm run dev`: Run in development mode with hot reload
- `npm run build`: Build TypeScript to JavaScript
- `npm start`: Run the built server
- `npm run setup <llm>`: Run the automated setup for specified LLM
- `npm run edit-env`: Configure environment variables

## Supported LLMs

- **Claude Desktop**: Full support with automated configuration
- **Cursor IDE**: Full support with automated configuration

## Error Handling

The server includes comprehensive error handling for:
- Configuration validation
- API endpoint connectivity
- Request/response validation
- Network timeouts
- Invalid parameters

All errors are properly formatted and returned to the AI assistant for appropriate handling.

## Authentication

Bearer token authentication is optional. If your API endpoints don't require authentication, simply leave the `TOKEN` environment variable unset or commented out in your `.env` file. The server will work without authentication headers.

## Troubleshooting

If the MCP server doesn't connect to your LLM:

1. **Check configuration**: Use `npm run edit-env --show` to verify your settings
2. **Verify endpoints**: Test your API endpoints independently
3. **Check logs**: Look at your LLM's logs for error messages
4. **Restart completely**: Completely restart your LLM after configuration changes
5. **Test server**: Run `node dist/index.js` to check for server errors
6. **Permissions**: Ensure your LLM has permission to execute Node.js scripts

## Example Usage

After setup, you can test the integration:

**Saving context:**
```
"My favorite programming language is TypeScript and I work as a senior developer at TechCorp."
```

**Searching context:**
```
"What's my favorite programming language?"
```

The MCP server will automatically handle saving and retrieving this information through your configured API endpoints.

## Installation from npm

```bash
# Install globally
npm install -g archivenet

# Configure environment interactively
edit-env --interactive

# Setup for your preferred LLM
setup-mcp claude
# or
setup-mcp cursor

# Start the server manually if needed
archivenet
```

## Project Structure

```
archivenet/
├── src/                    # TypeScript source files
├── dist/                   # Compiled JavaScript files
├── scripts/                # Setup and utility scripts
│   ├── setup-mcp.js       # Unified setup script for Claude and Cursor
│   └── edit-env.js        # Environment configuration editor
├── .env.example           # Environment variables template
├── package.json           # Package configuration
├── tsconfig.json          # TypeScript configuration
├── README.md              # This file
├── LICENSE                # MIT License
└── CHANGELOG.md           # Version history
```