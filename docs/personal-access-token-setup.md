# Using Personal Access Token with MCP Server

This guide explains how to configure your MCP (Model Context Protocol) server to use a Personal Access Token (PAT) for advanced control and authentication with GitHub.

## Why Use a Personal Access Token?

Personal Access Tokens provide more granular control over permissions and can be used instead of OAuth for authentication. This is particularly useful when you need specific scopes or want to manage authentication separately.

## Prerequisites

- VS Code with MCP server support
- A GitHub account

## Steps to Configure PAT

### Step 1: Create a Personal Access Token

1. Go to your GitHub account settings
2. Navigate to **Developer settings** > **Personal access tokens** > **Tokens (classic)**
3. Click **Generate new token** > **Generate new token (classic)**
4. Give your token a descriptive name
5. Select the following scopes:
   - `repo` - Full control of private repositories
   - `read:packages` - Download packages from GitHub Package Registry
6. Click **Generate token**
7. **Important:** Copy the token immediately - you won't be able to see it again!

### Step 2: Configure MCP Server

You'll follow the same setup steps as before, but **cancel OAuth when prompted**.

### Step 3: Add Authorization Header

In your MCP server configuration file, add the authorization header:

```json
"headers": {
  "Authorization": "Bearer ${input:github_token}"
}
```

This configuration uses a variable reference `${input:github_token}` that will prompt you to enter your token securely.

### Step 4: Add Input Prompt

Next, add an input prompt configuration to securely enter your token:

```json
"inputs": [
  {
    "id": "github_token",
    "type": "promptString",
    "description": "GitHub Personal Access Token",
    "password": true
  }
]
```

The `password: true` setting ensures that your token is masked when you enter it.

### Step 5: Restart and Authenticate

1. Restart the MCP server in VS Code
2. When prompted, enter your Personal Access Token
3. The MCP server will now use the PAT for authorization

## Complete Configuration Example

Here's a complete example of what your configuration might look like:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "headers": {
        "Authorization": "Bearer ${input:github_token}"
      }
    }
  },
  "inputs": [
    {
      "id": "github_token",
      "type": "promptString",
      "description": "GitHub Personal Access Token",
      "password": true
    }
  ]
}
```

## Security Best Practices

- **Never commit your PAT to version control** - Always use secure input prompts or environment variables
- **Use minimal scopes** - Only grant the permissions your application actually needs
- **Rotate tokens regularly** - Generate new tokens periodically and revoke old ones
- **Revoke unused tokens** - If you're no longer using a token, revoke it in your GitHub settings
- **Store securely** - If you must store your token, use a secure password manager

## Troubleshooting

### Token Not Accepted
- Verify the token has the correct scopes (`repo` and `read:packages`)
- Make sure you copied the entire token without any extra spaces
- Check that the token hasn't expired

### MCP Server Won't Start
- Ensure the configuration JSON is valid (no syntax errors)
- Verify the `${input:github_token}` reference matches the input `id` exactly
- Restart VS Code completely if the MCP server doesn't recognize the new configuration

### Authentication Errors
- Double-check that you entered the token correctly when prompted
- Try generating a new token if the current one seems invalid
- Ensure your GitHub account has access to the resources you're trying to access

## Additional Resources

- [GitHub Personal Access Tokens Documentation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [Model Context Protocol Documentation](https://modelcontextprotocol.io/)
- [VS Code Settings Documentation](https://code.visualstudio.com/docs/getstarted/settings)

## What's Next?

Once your MCP server is configured with your PAT, you can start using GitHub Copilot with enhanced access to your repositories and packages. The server will authenticate all requests using your Personal Access Token.
