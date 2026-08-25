

# Redis MCP Server
[![smithery badge](https://smithery.ai/badge/redis-mcp)](https://smithery.ai/server/redis-mcp)

A Model Context Protocol (MCP) server that provides access to Redis database operations.

<a href="https://glama.ai/mcp/servers/cbn7lsbp7h"><img width="380" height="200" src="https://glama.ai/mcp/servers/cbn7lsbp7h/badge" alt="Redis Server MCP server" /></a>

## Project Structure

```
src/
├── interfaces/
│   └── types.ts           # Shared TypeScript interfaces and types
├── tools/
│   ├── base_tool.ts       # Abstract base class for Redis tools
│   ├── tool_registry.ts   # Registry managing all available Redis tools
│   ├── hmset_tool.ts      # HMSET Redis operation
│   ├── hget_tool.ts       # HGET Redis operation
│   ├── hgetall_tool.ts    # HGETALL Redis operation
│   ├── scan_tool.ts       # SCAN Redis operation
│   ├── set_tool.ts        # SET Redis operation
│   ├── get_tool.ts        # GET Redis operation
│   ├── del_tool.ts        # DEL Redis operation
│   ├── zadd_tool.ts       # ZADD Redis operation
│   ├── zrange_tool.ts     # ZRANGE Redis operation
│   ├── zrangebyscore_tool.ts # ZRANGEBYSCORE Redis operation
│   └── zrem_tool.ts       # ZREM Redis operation
└── redis_server.ts        # Main server implementation
```

## Available Tools

| Tool | Type | Description | Input Schema |
|------|------|-------------|--------------|
| hmset | Hash Command | Set multiple hash fields to multiple values | `key`: string (Hash key)<br>`fields`: object (Field-value pairs to set) |
| hget | Hash Command | Get the value of a hash field | `key`: string (Hash key)<br>`field`: string (Field to get) |
| hgetall | Hash Command | Get all fields and values in a hash | `key`: string (Hash key) |
| scan | Key Command | Scan Redis keys matching a pattern | `pattern`: string (Pattern to match, e.g., "user:*")<br>`count`: number, optional (Number of keys to return, maximum 10) |
| set | String Command | Set string value with optional NX and PX options | `key`: string (Key to set)<br>`value`: string (Value to set)<br>`nx`: boolean, optional (Only set if not exists)<br>`px`: number, optional (Expiry in milliseconds) |
| get | String Command | Get string value | `key`: string (Key to get) |
| del | Key Command | Delete a key | `key`: string (Key to delete) |
| zadd | Sorted Set Command | Add one or more members to a sorted set | `key`: string (Sorted set key)<br>`members`: array of objects with `score`: number and `value`: string |
| zrange | Sorted Set Command | Return a range of members from a sorted set by index | `key`: string (Sorted set key)<br>`start`: number (Start index)<br>`stop`: number (Stop index)<br>`withScores`: boolean, optional (Include scores in output) |
| zrangebyscore | Sorted Set Command | Return members from a sorted set with scores between min and max | `key`: string (Sorted set key)<br>`min`: number (Minimum score)<br>`max`: number (Maximum score)<br>`withScores`: boolean, optional (Include scores in output) |
| zrem | Sorted Set Command | Remove one or more members from a sorted set | `key`: string (Sorted set key)<br>`members`: array of strings (Members to remove) |
| sadd | Set Command | Add one or more members to a set | `key`: string (Set key)<br>`members`: array of strings (Members to add to the set) |
| smembers | Set Command | Get all members in a set | `key`: string (Set key) |

## Usage

Configure in your MCP client (e.g., Claude Desktop, Cline):

```json
{
  "mcpServers": {
    "redis": {
      "command": "npx",
      "args": ["redis-mcp", "--redis-host", "localhost", "--redis-port", "6379"],
      "disabled": false
    }
  }
}
```

## Command Line Arguments

- `--redis-host`: Redis server host (default: localhost)
- `--redis-port`: Redis server port (default: 6379)

### Installing via Smithery

To install Redis Server for Claude Desktop automatically via [Smithery](https://smithery.ai/server/redis-mcp):

```bash
npx -y @smithery/cli install redis-mcp --client claude
```

## Development

To add a new Redis tool:

1. Create a new tool class in `src/tools/` extending `RedisTool`
2. Define the tool's interface in `src/interfaces/types.ts`
3. Register the tool in `src/tools/tool_registry.ts`

Example tool implementation:

```typescript
export class MyTool extends RedisTool {
  name = 'mytool';
  description = 'Description of what the tool does';
  inputSchema = {
    type: 'object',
    properties: {
      // Define input parameters
    },
    required: ['requiredParam']
  };

  validateArgs(args: unknown): args is MyToolArgs {
    // Implement argument validation
  }

  async execute(args: unknown, client: RedisClientType): Promise<ToolResponse> {
    // Implement tool logic
  }
}
```



## Running evals

The evals package loads an mcp client that then runs the index.ts file, so there is no need to rebuild between tests. You can load environment variables by prefixing the npx command. Full documentation can be found [here](https://www.mcpevals.io/docs).

```bash
OPENAI_API_KEY=your-key  npx mcp-eval src/evals/evals.ts src/tools/zrangebyscore_tool.ts
```
## License

MIT: https://opensource.org/license/mit
