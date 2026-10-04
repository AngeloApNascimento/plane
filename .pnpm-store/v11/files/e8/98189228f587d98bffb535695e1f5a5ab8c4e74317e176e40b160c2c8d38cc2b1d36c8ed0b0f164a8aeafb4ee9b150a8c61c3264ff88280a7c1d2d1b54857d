//#region src/mcp/types.d.ts
type McpAgentType = "antigravity" | "cline" | "cline-cli" | "claude-code" | "claude-desktop" | "codex" | "cursor" | "gemini-cli" | "goose" | "github-copilot-cli" | "mcporter" | "opencode" | "vscode" | "zed";
type McpConfigFormat = "json" | "jsonc" | "yaml" | "toml";
type McpTransportType = "http" | "sse" | "stdio";
type McpRemoteTransport = "http" | "sse";
type McpSourceType = "remote" | "package" | "command";
interface ParsedMcpSource {
  type: McpSourceType;
  value: string;
  inferredName: string;
}
interface McpServerConfig {
  type?: McpRemoteTransport;
  url?: string;
  headers?: Record<string, string>;
  command?: string;
  args?: string[];
  env?: Record<string, string>;
}
interface McpAgentConfig {
  name: McpAgentType;
  displayName: string;
  globalConfigPath: string;
  projectConfigPath?: string;
  configKey: string;
  projectConfigKey?: string;
  format: McpConfigFormat;
  supportedTransports: readonly McpTransportType[];
  unsupportedTransportMessage?: string;
  detectGlobalInstall: () => boolean;
  detectProjectInstall?: (cwd: string) => boolean;
  resolveConfigPath?: (options: {
    global: boolean;
    cwd: string;
  }) => string;
  transformConfig?: (serverName: string, config: McpServerConfig, context: {
    global: boolean;
  }) => unknown;
}
interface InstallMcpServerOptions {
  source: string;
  name?: string;
  agents?: McpAgentType[];
  global?: boolean;
  cwd?: string;
  transport?: McpRemoteTransport;
  headers?: Record<string, string>;
  env?: Record<string, string>;
}
interface McpInstallResultForAgent {
  agent: McpAgentType;
  success: boolean;
  path: string;
  error?: string;
}
interface InstallMcpServerResult {
  serverName: string;
  config: McpServerConfig;
  results: McpInstallResultForAgent[];
}
interface ListedMcpServer {
  serverName: string;
  agent: McpAgentType;
  path: string;
  config: unknown;
}
interface RemoveMcpServerOptions {
  name: string;
  agents?: McpAgentType[];
  global?: boolean;
  cwd?: string;
}
interface RemoveMcpServerResult {
  agent: McpAgentType;
  path: string;
  removed: boolean;
  error?: string;
}
//#endregion
//#region src/mcp/agents.d.ts
declare const mcpAgents: Record<McpAgentType, McpAgentConfig>;
declare const mcpAgentAliases: Record<string, McpAgentType>;
declare const getMcpAgentConfig: (agentType: McpAgentType) => McpAgentConfig;
declare const getMcpAgentTypes: () => McpAgentType[];
declare const isMcpAgentType: (value: string) => value is McpAgentType;
declare const resolveMcpAgentAlias: (input: string) => McpAgentType | null;
declare const isMcpTransportSupported: (agent: McpAgentConfig, transport: McpTransportType) => boolean;
declare const detectProjectInstalledMcpAgents: (cwd: string) => McpAgentType[];
declare const detectGloballyInstalledMcpAgents: () => McpAgentType[];
declare const getMcpAgentsSupportingProjectScope: () => McpAgentType[];
//#endregion
//#region src/mcp/build-server-config.d.ts
interface BuildMcpServerConfigOptions {
  transport?: McpRemoteTransport;
  headers?: Record<string, string>;
  env?: Record<string, string>;
}
declare const buildMcpServerConfig: (parsed: ParsedMcpSource, options?: BuildMcpServerConfigOptions) => McpServerConfig;
//#endregion
//#region src/mcp/constants.d.ts
declare const DEFAULT_REMOTE_TRANSPORT: "http";
declare const NPX_COMMAND = "npx";
declare const NPX_DASH_Y = "-y";
//#endregion
//#region src/mcp/formats/index.d.ts
declare const readConfigFile: (filePath: string, format: McpConfigFormat) => Record<string, unknown>;
declare const writeServerToConfigFile: (filePath: string, format: McpConfigFormat, dottedKey: string, serverName: string, serverConfig: unknown) => void;
declare const removeServerFromConfigFile: (filePath: string, format: McpConfigFormat, dottedKey: string, serverName: string) => boolean;
declare const listServersInConfigFile: (filePath: string, format: McpConfigFormat, dottedKey: string) => Record<string, unknown>;
//#endregion
//#region src/mcp/install-mcp-server.d.ts
interface ResolvedTargetAgents {
  agents: McpAgentType[];
  detected: boolean;
}
declare const resolveMcpTargetAgents: (requested: McpAgentType[] | undefined, isGlobal: boolean, cwd: string) => ResolvedTargetAgents;
declare const installMcpServer: (options: InstallMcpServerOptions) => InstallMcpServerResult;
//#endregion
//#region src/mcp/installer.d.ts
interface InstallMcpServerForAgentOptions {
  global?: boolean;
  cwd?: string;
}
declare const installMcpServerForAgent: (serverName: string, serverConfig: McpServerConfig, agentType: McpAgentType, options?: InstallMcpServerForAgentOptions) => McpInstallResultForAgent;
declare const installMcpServerForAgents: (serverName: string, serverConfig: McpServerConfig, agentTypes: McpAgentType[], options?: InstallMcpServerForAgentOptions) => McpInstallResultForAgent[];
//#endregion
//#region src/mcp/resolve-config-target.d.ts
interface McpConfigTarget {
  configPath: string;
  configKey: string;
}
interface ResolveMcpConfigTargetOptions {
  global?: boolean;
  cwd?: string;
}
declare const resolveMcpConfigTarget: (agent: McpAgentConfig, options?: ResolveMcpConfigTargetOptions) => McpConfigTarget;
//#endregion
//#region src/mcp/list.d.ts
interface ListInstalledMcpServersOptions {
  agents?: McpAgentType[];
  global?: boolean;
  cwd?: string;
}
declare const listInstalledMcpServers: (options?: ListInstalledMcpServersOptions) => ListedMcpServer[];
//#endregion
//#region src/mcp/source-parser.d.ts
declare const extractPackageName: (input: string) => string;
declare const parseMcpSource: (input: string) => ParsedMcpSource;
declare const isRemoteMcpSource: (parsed: ParsedMcpSource) => boolean;
//#endregion
//#region src/mcp/remove.d.ts
declare const removeMcpServerFromAgent: (serverName: string, agentType: McpAgentType, options?: {
  global?: boolean;
  cwd?: string;
}) => RemoveMcpServerResult;
declare const removeMcpServer: (options: RemoveMcpServerOptions) => RemoveMcpServerResult[];
declare namespace index_d_exports {
  export { DEFAULT_REMOTE_TRANSPORT, InstallMcpServerOptions, InstallMcpServerResult, ListedMcpServer, McpAgentConfig, McpAgentType, McpConfigFormat, McpInstallResultForAgent, McpRemoteTransport, McpServerConfig, McpSourceType, McpTransportType, NPX_COMMAND, NPX_DASH_Y, ParsedMcpSource, RemoveMcpServerOptions, RemoveMcpServerResult, installMcpServer as add, buildMcpServerConfig, detectGloballyInstalledMcpAgents, detectProjectInstalledMcpAgents, extractPackageName, getMcpAgentConfig, getMcpAgentTypes, getMcpAgentsSupportingProjectScope, installMcpServer as install, installMcpServer, installMcpServerForAgent, installMcpServerForAgents, isMcpAgentType, isMcpTransportSupported, isRemoteMcpSource, listInstalledMcpServers as list, listInstalledMcpServers, listServersInConfigFile, mcpAgentAliases, mcpAgents, parseMcpSource, parseMcpSource as parseSource, readConfigFile, removeMcpServer as remove, removeMcpServer, removeMcpServerFromAgent, removeServerFromConfigFile, resolveMcpAgentAlias, resolveMcpConfigTarget, resolveMcpTargetAgents, writeServerToConfigFile };
}
//#endregion
export { resolveMcpAgentAlias as A, McpSourceType as B, getMcpAgentConfig as C, isMcpTransportSupported as D, isMcpAgentType as E, McpAgentType as F, ParsedMcpSource as H, McpConfigFormat as I, McpInstallResultForAgent as L, InstallMcpServerResult as M, ListedMcpServer as N, mcpAgentAliases as O, McpAgentConfig as P, McpRemoteTransport as R, detectProjectInstalledMcpAgents as S, getMcpAgentsSupportingProjectScope as T, RemoveMcpServerOptions as U, McpTransportType as V, RemoveMcpServerResult as W, DEFAULT_REMOTE_TRANSPORT as _, isRemoteMcpSource as a, buildMcpServerConfig as b, resolveMcpConfigTarget as c, installMcpServer as d, resolveMcpTargetAgents as f, writeServerToConfigFile as g, removeServerFromConfigFile as h, extractPackageName as i, InstallMcpServerOptions as j, mcpAgents as k, installMcpServerForAgent as l, readConfigFile as m, removeMcpServer as n, parseMcpSource as o, listServersInConfigFile as p, removeMcpServerFromAgent as r, listInstalledMcpServers as s, index_d_exports as t, installMcpServerForAgents as u, NPX_COMMAND as v, getMcpAgentTypes as w, detectGloballyInstalledMcpAgents as x, NPX_DASH_Y as y, McpServerConfig as z };