# MCP Redmine

**Let Claude/Cursor be your Redmine assistant! MCP Redmine connects Claude/Cursor to your Redmine instance, allowing it to:**
- Search and browse projects and issues
- Create and update issues with full markdown support
- Upload and download file attachments
- Manage and track time entries
- Update issue statuses and fields
- Access comprehensive Redmine API functionality

Uses httpx for [Redmine API](https://petstore.swagger.io/?url=https%3A%2F%2Fraw.githubusercontent.com%2FLerrrtaste%2Fredmine-openapi-specification%2Fmain%2Fredmine_openapi.yaml) requests and integrates with the Redmine OpenAPI specification for comprehensive API coverage.

## We can run MCP server locally or in docker container. 
- How to run MCP server: [Local MCP server](#local-MCP-server)
- How to run MCP server: [Docker MCP server](#docker-mcp-server)

## Local MCP server
- Install python -> [Download](https://www.python.org/ftp/python/pymanager/python-manager-25.2.msix)
- Clone repository -> ```git clone https://github.com/viacheslavdemchenko-create/mcp-redmine.git```
- Prepare `.env` variables
  - REDMINE_URL -> [Getting Redmine URL](#getting-redmine-url)
  - REDMINE_API_KEY -> [Getting Redmine API Key](#getting-redmine-api-key)
  - REDMINE_REQUEST_INSTRUCTIONS
  - REDMINE_ALLOWED_DIRECTORIES
- Install `uv` python package manager -> [uv Instalation guide](#uv-instalation-guide)
  - Create virtual environment `uv venv`
  - Activate virtual environment:
    - Windows: `.venv\Scripts\Activate.ps1`
    - macOS: `source ./venv/bin/activate`
  - Install dependencies `uv pip install -e .`
- Run MCP Server `uv run mcp_redmine\server.py`


## Usage with Cursor

1. Click `Ctrl+Shift+P` to open command palette
2. Type: `Open MCP Settings` and click `Enter`
3. Select on the left hand side: `Tools * MCP`
4. Click: `New MCP Server`
5. Add to your `mcp.json`:
```json
{
  "mcpServers": {
    "redmine": {
      "type": "sse",
      "url": "http://127.0.0.1:8000/sse"
    }
"redmine": {
        "command": "uvx",
        "args": ["--from", "mcp-redmine==2026.01.13.152335",
                "--refresh-package", "mcp-redmine", "mcp-redmine"],
        "env": {
          "REDMINE_URL": "https://your-redmine-instance.example.com",
          "REDMINE_API_KEY": "your-api-key",
          "REDMINE_REQUEST_INSTRUCTIONS": "/path/to/instructions.md",
          "REDMINE_ALLOWED_DIRECTORIES": "/tmp,/home/user/uploads"
        }
      }
  }
}
```

## Usage with Claude Desktop

Add to your `claude_desktop_config.json`:
```json
  {
    "mcpServers": {
      "redmine": {
        "command": "uvx",
        "args": ["--from", "mcp-redmine==2026.01.13.152335",
                "--refresh-package", "mcp-redmine", "mcp-redmine"],
        "env": {
          "REDMINE_URL": "https://your-redmine-instance.example.com",
          "REDMINE_API_KEY": "your-api-key",
          "REDMINE_REQUEST_INSTRUCTIONS": "/path/to/instructions.md",
          "REDMINE_ALLOWED_DIRECTORIES": "/tmp,/home/user/uploads"
        }
      }
    }
  }
```

## Docker MCP server
- Clone repository -> ```git clone https://github.com/viacheslavdemchenko-create/mcp-redmine.git```
- Prepare `.env` variables
  - REDMINE_URL -> [Getting Redmine URL](#getting-redmine-url)
  - REDMINE_API_KEY -> [Getting Redmine API Key](#getting-redmine-api-key)
  - REDMINE_REQUEST_INSTRUCTIONS
  - REDMINE_ALLOWED_DIRECTORIES

Ensure you have docker installed. 
```bash
docker --version
```

Build docker image:
```bash
git clone https://github.com/viacheslavdemchenko-create/mcp-redmine.git
cd mcp-redmine
docker build -t mcp-redmine .
```

## Usage with Cursor

1. Click `Ctrl+Shift+P` to open command palette
2. Type: `Open MCP Settings` and click `Enter`
3. Select on the left hand side: `Tools * MCP`
4. Click: `New MCP Server`
5. Add to your `mcp.json`:
  ```json
  {
    "mcpServers": {
      "redmine": {
        "command": "docker",
        "args":  [
            "run",
            "-i",
            "--rm",
            "-e", "REDMINE_URL",
            "-e", "REDMINE_API_KEY",
            "-e", "REDMINE_REQUEST_INSTRUCTIONS",
            "-e", "REDMINE_ALLOWED_DIRECTORIES",
            "-v", "/path/to/instructions.md:/app/INSTRUCTIONS.md",
            "-v", "/path/to/uploads:/app/uploads",
            "mcp-redmine"
        ],
        "env": {
          "REDMINE_URL": "https://your-redmine-instance.example.com",
          "REDMINE_API_KEY": "your-api-key",
          "REDMINE_REQUEST_INSTRUCTIONS": "/app/INSTRUCTIONS.md",
          "REDMINE_ALLOWED_DIRECTORIES": "/app/uploads"
        }
      }
    }
  }
  ```

## Usage with Claude Desktop

Add to your `claude_desktop_config.json`:
  ```json
  {
    "mcpServers": {
      "redmine": {
        "command": "docker",
        "args":  [
            "run",
            "-i",
            "--rm",
            "-e", "REDMINE_URL",
            "-e", "REDMINE_API_KEY",
            "-e", "REDMINE_REQUEST_INSTRUCTIONS",
            "-e", "REDMINE_ALLOWED_DIRECTORIES",
            "-v", "/path/to/instructions.md:/app/INSTRUCTIONS.md",
            "-v", "/path/to/uploads:/app/uploads",
            "mcp-redmine"
        ],
        "env": {
          "REDMINE_URL": "https://your-redmine-instance.example.com",
          "REDMINE_API_KEY": "your-api-key",
          "REDMINE_REQUEST_INSTRUCTIONS": "/app/INSTRUCTIONS.md",
          "REDMINE_ALLOWED_DIRECTORIES": "/app/uploads"
        }
      }
    }
  }
  ```

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `REDMINE_URL` | Yes | - | URL of your Redmine instance. |
| `REDMINE_API_KEY` | Yes | - | Your Redmine API key (see below for how to get it) |
| `REDMINE_REQUEST_INSTRUCTIONS` | No | - | Path to a file containing additional instructions for the redmine_request tool. I've found it works great to have the LLM generate that file after a session. ([example1](INSTRUCTIONS_EXAMPLE1.md) [example2](INSTRUCTIONS_EXAMPLE2.md)) |
| `REDMINE_HEADERS` | No | (empty) | Custom HTTP headers to include in all requests. Format: `"Header1: Value1, Header2: Value2"`. Useful for proxies that require additional authentication (e.g., `X-Redmine-Username`) |
| `REDMINE_RESPONSE_FORMAT` | No | `yaml` | Response format: `yaml` or `json`. Controls how API responses are formatted |
| `REDMINE_ALLOWED_DIRECTORIES` | For upload/download | (disabled) | **Required for file operations.** Comma-separated list of directories where upload/download are allowed (e.g., `/tmp,/home/user/uploads`). Upload/download are disabled if not set for security |
| `REDMINE_DANGEROUSLY_ACCEPT_INVALID_CERTS` | No | (disabled) | Set to `1` to disable SSL certificate verification. Use only for self-signed certs in trusted environments |

> **Note**: When running via Docker, the `REDMINE_REQUEST_INSTRUCTIONS` environment variable must point to a **path inside the container**, not a path on the host machine.
> Therefore, if you want to use a local file, you need to **mount it into the container** at the correct location.

> **Security Note**: The `REDMINE_ALLOWED_DIRECTORIES` setting protects against path traversal attacks. Paths containing `../` are resolved before validation, ensuring files can only be accessed within the allowed directories.

## uv Instalation guide
### 1. Install `uv`

- Linux
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

- macOS
  ```zsh
  brew install uv
  ```

- windows
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```

Ensure you have uv installed.
```bash
uv --version
```

## Getting Redmine URL

1. Copy Redmine domain part to use in your configuration

## Getting Redmine API Key

1. Log in to your Redmine instance
2. On the top-right corner click human icon
3. Click "My account/Moje konto" section
4. If right sidebar hidden click on 3 dots icon. On the right side of the page, you should see "API access key/Klucz dostępu do API"
5. Click "Show/Pokaz" to view your existing key or "Generate" to create a new one
6. Copy this key for use in your configuration

## API

### Tools

- **redmine_paths_list**
  - Return a list of available API paths from OpenAPI spec
  - No input required
  - Returns a YAML string containing a list of path templates:
  ```
  - /issues.json
  - /projects.json
  - /time_entries.json
  ...
  ```

- **redmine_paths_info**
  - Get full path information for given path templates
  - Input: `path_templates` (list of strings)
  - Returns YAML string containing API specifications for the requested paths:
  ```yaml
  /issues.json:
    get:
      operationId: getIssues
      parameters:
        - $ref: '#/components/parameters/format'
      ...
  ```

- **redmine_request**
  - Make a request to the Redmine API
  - Inputs:
    - `path` (string): API endpoint path (e.g. '/issues.json')
    - `method` (string, optional): HTTP method to use (default: 'get')
    - `data` (object, optional): Dictionary for request body (for POST/PUT)
    - `params` (object, optional): Dictionary for query parameters
  - Returns YAML string containing response status code, body and error message:
  ```yaml
  status_code: 200
  body:
    issues:
      - id: 1
        subject: "Fix login page"
        ...
  error: ""
  ```

- **redmine_upload**
  - Upload a file to Redmine and get a token for attachment
  - **Requires `REDMINE_ALLOWED_DIRECTORIES` to be set**
  - Inputs:
    - `file_path` (string): Fully qualified path to the file to upload (must be within allowed directories)
    - `description` (string, optional): Optional description for the file
  - Returns YAML string with the same format as redmine_request, including upload token:
  ```yaml
  status_code: 201
  body:
    upload:
      id: 7
      token: "7.ed32257a2ab0f7526c0d72c32994c58b131bb2c0775f7aa84aae01ea8397ea54"
  error: ""
  ```

- **redmine_download**
  - Download an attachment from Redmine and save it to a local file
  - **Requires `REDMINE_ALLOWED_DIRECTORIES` to be set**
  - Inputs:
    - `attachment_id` (integer): The ID of the attachment to download
    - `save_path` (string): Fully qualified path where the file should be saved (must be within allowed directories)
    - `filename` (string, optional): Optional filename to use (determined automatically if not provided)
  - Returns YAML string with download results:
  ```yaml
  status_code: 200
  body:
    saved_to: "/path/to/downloaded/file.pdf"
    filename: "file.pdf"
  error: ""
  ```

## Examples

### Creating a new issue

```
Let's create a new bug report in the "Website" project:

1. Title: "Homepage not loading on mobile devices"
2. Description: "When accessing the homepage from iOS or Android devices, the loading spinner appears but the content never loads. This issue started after the last deployment."
3. Priority: High
4. Assign to: John Smith
```

### Searching for issues

```
Can you find all high priority issues in the "Website" project that are currently unassigned?
```

### Updating issue status

```
Please mark issue #123 as "In Progress" and add a comment: "I've started working on this issue. Expect it to be completed by Friday."
```

### Logging time

```
Log 3.5 hours against issue #456 for "Implementing user authentication" done today.
```
