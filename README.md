# BlackDuck SCA Plugin

## Description
An MCP server and skills for the Black Duck Software Composition Analysis (SCA) - BDSCA. 
Provides comprehensive project management, vulnerability tracking, policy compliance, 
and component analysis capabilities.

## Installation and Configuration
### Prerequisites
Up and running Black Duck BDSCA server with user account and created access token.

### Installation
```bash
# set environment variables for the session
$ claude
# add the marketplace entry for the server (only needed once)
/plugin install blackduck-bdsca@blackducksoftware/llm-plugins
/reload-plugins
What SCA tools do you have access to?
Show me my latest scans and vulnerabilities.
```

For more examples on the usage of the MCP server, see the **[MCP README.md](https://github.com/blackducksoftware/sca-mcp/blob/main/README.md)**.

### Environment Variables
Available environment variables for configuring the MCP server:

| Variable                                                           | Default             | Description                                                             |
|--------------------------------------------------------------------|---------------------|-------------------------------------------------------------------------|
| `BLACKDUCK_BDSCA_URL`                                              | `https://localhost` | Base URL of the Black Duck BDSCA instance                               |
| `BLACKDUCK_BDSCA_TOKEN`                                            | *(required)*        | API bearer token for BDSCA authentication                               |
| `BLACKDUCK_BDSCA_SSL_VERIFY`                                       | `true`              | Whether to verify SSL/TLS certificates for BDSCA connections             |
| `BLACKDUCK_BDSCA_LOG_LEVEL`                                        | `INFO`              | Logging level: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`          |
| `BLACKDUCK_BDSCA_LOG_FORMAT`                                       | `colored`           | Log format: `colored`, `json`, `simple`                                 |
| `BLACKDUCK_BDSCA_LOG_FILE`                                         | *(none)*            | Path to a log file (written in addition to stderr)                      |


## Skills
The plugin includes user-invoked **skills** that orchestrate the MCP
tools into complete security workflows.  
For detailed skill descriptions see **[skills/README.md](skills/README.md)**.

## Authors and acknowledgment
Jussi Polet <polet@blackduck.com>  
Matti Siipola <matti@blackduck.com>  
Bogdan Mihaila <bmihaila@blackduck.com>  

## License
Copyright (C) 2026 Black Duck Software, Inc. - see LICENSE file for details. All rights reserved.
