### Background

A coding agent is an advanced AI tool that automates and assists with various software development tasks, going beyond simple code generation to handle multi-step, contextual challenges. Unlike earlier AI code assistants that primarily offered code completion and text-based chat, a coding agent can take an instruction in plain language (e.g., "Build a to-do app" or "Fix this bug") and break it down, write code, run tests, and adapt based on the results.

Developers can expand the context-awareness of a coding agent by connecting it to 1 or more Model Context Protocol (MCP) servers.  The specification, released in November 2024 by Anthropic,  provides a standardized way to connect AI agents to different data sources and tools.   MCP Hosts are AI-powered applications support accessing functions and data over MCP.  They consume these services by opening an MCP Client connection to an MCP Server.  An MCP Server is a lightweight program that makes tools, resources and prompts available to the agent.

- Resources are represented by URIs and can be resolved to streamable content and may support subscriptions
- Tools are functions invocable by an Agent.  Their inputs and outputs are described via a json schema.  The descriptions of the function, input and outputs are used by the agent to determine when to invoke them, how to construct input and how to interpret output.
- Prompts are templates for constructing input to an Agent.
- For an intro to other features, consult the MCP specification
	
MCP servers can be accessed locally over STDIO or remotely over HTTP, and these servers can be served from within an IDE's process.
Editors and IDEs and can integrate Coding Agents into their user experience by adopting the Agent Client Protocol.   Open sourced from a collaboration between the Zed Editor and Google Gemini CLI teams, this protocol defines a JSONRPC contract between an editor client and a coding agent.  Features of the protocol include:

- Chat Session Lifecycle
- User Prompt Lifecycle
- Client can forward list of MCP Servers to Agent
- Toggling Session Modes and Models
- Tool Use Confirmation
- API for read/write file/editor buffers
- Embedded and referenced resources
- / # @ commands
	
The list of agents that support the protocol or have 3rd party adapters include:

- Gemini CLI
- Claude Code (via Zed’s SDK adapter)
- Codex CLI (via Zed’s adapter)
- Goose
	
The list of editors / IDEs that support the protocol or have adapters include:

- Zed
- JetBrains (coming soon)
- Emacs via agent-shell.el
- neovim

 
### Scope

We propose adding two features to the Eclipse IDE Ecosystem that are implementations of the Model Context Protocol (MCP)  and Agent Client Protocol (ACP).
The first feature will add support for running a Model Context Protocol server within the Eclipse IDE VM.  Prompts, Tools and Resources running on these servers will be able to interact with any public API running in the Eclipse instance, enabling coding agents to interact with elements of the IDE and its workspace.  An extension point and annotations framework will enable plugins to contribute tools, resource controllers, and prompts to the IDE MCP server.

The second feature will add a Coding Agents view that implements the Agent-Client Protocol specification.  It will feature an in-IDE chat experience where users can prompt one of several agents and receive responses.  Agents will be able to modify Text Editor documents as will as files in the workspace.  Agents will be able to run tools and commands approved by the user.  
The scope of these two features will be bounded by the scopes of the MCP and ACP protocols which are under a year old and are still evolving.

### Description

The Scope of an MVP would include:
- Agent Contexts
    - A default MCP server that can be enabled to serve over an HTTP port from within the Eclipse IDE
    - A set of built-in platform MCP tools and resources, including:
        - Tools granting access to workspace, editors, consoles, markers, and annotations, …
        - Resource Providers granting access to workspace, editors, and consoles, ...
    - An extension point for platform and 3rd party plugins to contribute MCP tools, resources and prompts to the Eclipse MCP server.
        - Plugins contribute MCP resources by annotating Java functions a combination of with MCP Annotations and Jackson Annotations
    - Centralized preferences, tracing, and capabilities
        - Users can customize which of the installed MCP tools are in scope for an activity using Capabilities or perhaps on a tool by tool basis.
- Coding Agents
    - A Coding Agent view that supports entering prompts to and displaying responses from a coding agent
        - Ability to forward text, resource links, embedded resources, perhaps images, and perhaps audio to a coding agent.
        - Ability to display rich content such as Markdown, Styled Code Blocks, and perhaps Mermaid Diagrams
        - Ability to connect to one or more Coding Agent
            - Ability to customize agent startup commands
            - Ability to automatically download and update coding agent to an .eclipseagents folder in the user's home directory
            - Perhaps ability to customize startup commands to invoke any custom coding agent
        - Ability to start a new chat session
            - Ability to automatically configure chat session with select MCP servers
        - Ability for coding agent to edit workspace files and Text Editor synchronized documents.
        - Ability to add projects, folders, files and selected text to a chat session
        - Ability to use / & # shortcut commands with content assistance
        - Ability for user to apply granular approval for agent tool usage
        - Keyboard and Screen Reader accessibility
        - Initial choice of agent(s) will likely lean towards one with good performance, good onboarding, and enough free usage to evaluate the overall experience.

### Why Create a new Eclipse Project:

Adding an open source, extensible MCP Server framework will enable the platform, third-parties and end-users to easily instrument features for Coding Agent interactivity.  These services can be consumed by any MCP Host such as stand-alone applications like Claude Desktop, command-line CLIs like Google Gemini (which may be run in an external terminal or in the Eclipse Terminal view, and in-IDE experiences such as Copilot for Eclipse and the proposed ACP-powered Coding Agents view.

An open source ACP Chat experience enables Coding Agents to have read and interact to the internal state of the IDE, and because the agents can accept a list of MCP servers as input, can connect to the IDE's MCP servers without the user having to copy/paste MCP urls into an agents JSON configuration file.  Re-using the wildwebdevloper's node manager means developers without node skills can consume npm powered coding agents without manual intervention.

### Future Work

A minimum viable product release may implement a subset of the available features in the ACP spec and support for a subset of the compatible coding agents.  Support for coding agents may be further limited to those applicable to Eclipse IDE use cases. 

Further features and agents can be adopted over time.  Both the MCP and ACP protocols are under a year old and are still evolving, and changes will need to be evaluated for currency and adoption.

Features that may be excluded from an MVP include:

- Support for additional Coding Agents out-of-the-box
- Load Prior Chat Sessions
- A registry for external local or remote MCP servers to forward to the coding agent.
- Tools to context-switch between sets of MCP services
- Support for changing the Mode and Model of a coding agent.
- Support for letting a coding agent proxy terminal commands through the IDE.
- Inclusion of additional built-in MCP tools within scope of this project
- Plugins outside the scope of this project contributing their own tools and resources to the MCP server using our extension point.
     - For example, Data Tools could contribute MCP tools to run sql, list tables, ...
- Elements cut from the current MVP scope due to time-constraints
