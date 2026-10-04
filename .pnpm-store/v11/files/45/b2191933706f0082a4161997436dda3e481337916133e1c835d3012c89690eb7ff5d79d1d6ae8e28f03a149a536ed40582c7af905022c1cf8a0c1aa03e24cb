import { t as __exportAll } from "./chunk-pbuEa-1d.js";
import { n as isPlainObject, t as toErrorMessage } from "./to-error-message-Bg0SEUet.js";
import { existsSync, mkdirSync, readFileSync, writeFileSync } from "node:fs";
import { homedir, platform } from "node:os";
import { dirname, join } from "node:path";
import { parse, stringify } from "yaml";
import { applyEdits, modify, parse as parse$1 } from "jsonc-parser";
import TOML from "@iarna/toml";
//#region src/mcp/transforms/codex.ts
const transformCodexServerConfig = (config) => {
	if (config.url) {
		const remoteConfig = {
			type: config.type || "http",
			url: config.url
		};
		if (config.headers && Object.keys(config.headers).length > 0) remoteConfig.headers = config.headers;
		return remoteConfig;
	}
	const stdioConfig = {
		command: config.command,
		args: config.args || []
	};
	if (config.env && Object.keys(config.env).length > 0) stdioConfig.env = config.env;
	return stdioConfig;
};
//#endregion
//#region src/mcp/constants.ts
const DEFAULT_REMOTE_TRANSPORT = "http";
const NPX_COMMAND = "npx";
const NPX_DASH_Y = "-y";
const MCP_DEFAULT_SERVER_NAME = "mcp-server";
const GENERIC_HOST_PREFIXES = new Set([
	"mcp",
	"api",
	"app",
	"www",
	"server",
	"servers",
	"remote"
]);
const COMMON_TLD_LABELS = new Set([
	"com",
	"org",
	"net",
	"io",
	"dev",
	"ai",
	"tech",
	"co",
	"app",
	"cloud",
	"sh",
	"run"
]);
const PACKAGE_NAME_PREFIX_STRIP = ["mcp-server-", "server-"];
const PACKAGE_NAME_SUFFIX_STRIP = ["-mcp-server", "-mcp"];
const KNOWN_COMMAND_RUNNERS = new Set([
	"npx",
	"node",
	"python",
	"python3",
	"uvx",
	"bunx",
	"deno"
]);
const SCRIPT_EXTENSION_REGEX = /\.(?:js|ts|mjs|cjs|py|sh|rb|go)$/i;
//#endregion
//#region src/mcp/transforms/goose.ts
const transformGooseServerConfig = (serverName, config) => {
	if (config.url) return {
		name: serverName,
		description: "",
		type: config.type === "sse" ? "sse" : "streamable_http",
		uri: config.url,
		headers: config.headers || {},
		enabled: true,
		timeout: 300
	};
	return {
		name: serverName,
		description: "",
		cmd: config.command,
		args: config.args || [],
		enabled: true,
		envs: config.env || {},
		type: "stdio",
		timeout: 300
	};
};
//#endregion
//#region src/mcp/transforms/opencode.ts
const transformOpenCodeServerConfig = (config) => {
	if (config.url) return {
		type: "remote",
		url: config.url,
		enabled: true,
		headers: config.headers
	};
	return {
		type: "local",
		command: [config.command, ...config.args || []],
		enabled: true,
		environment: config.env || {}
	};
};
//#endregion
//#region src/mcp/transforms/vscode.ts
const transformVscodeServerConfig = (config) => {
	if (config.url) {
		const remote = {
			type: config.type || "http",
			url: config.url
		};
		if (config.headers && Object.keys(config.headers).length > 0) remote.headers = config.headers;
		return remote;
	}
	const stdio = {
		type: "stdio",
		command: config.command,
		args: config.args || []
	};
	if (config.env && Object.keys(config.env).length > 0) stdio.env = config.env;
	return stdio;
};
//#endregion
//#region src/mcp/transforms/zed.ts
const transformZedServerConfig = (config) => {
	if (config.url) return {
		source: "custom",
		type: config.type || "http",
		url: config.url,
		headers: config.headers || {}
	};
	return {
		source: "custom",
		command: config.command,
		args: config.args || [],
		env: config.env || {}
	};
};
//#endregion
//#region src/mcp/agents.ts
const home = homedir();
const getPlatformPaths = () => {
	const currentPlatform = platform();
	if (currentPlatform === "win32") {
		const appData = process.env.APPDATA || join(home, "AppData", "Roaming");
		return {
			appSupport: appData,
			vscodePath: join(appData, "Code", "User"),
			gooseConfigPath: join(appData, "Block", "goose", "config", "config.yaml")
		};
	}
	if (currentPlatform === "darwin") return {
		appSupport: join(home, "Library", "Application Support"),
		vscodePath: join(home, "Library", "Application Support", "Code", "User"),
		gooseConfigPath: join(home, ".config", "goose", "config.yaml")
	};
	const configDir = process.env.XDG_CONFIG_HOME || join(home, ".config");
	return {
		appSupport: configDir,
		vscodePath: join(configDir, "Code", "User"),
		gooseConfigPath: join(configDir, "goose", "config.yaml")
	};
};
const { appSupport, vscodePath, gooseConfigPath } = getPlatformPaths();
const antigravityConfigPath = join(home, ".gemini", "antigravity", "mcp_config.json");
const clineCliConfigPath = join(process.env.CLINE_DIR || join(home, ".cline"), "data", "settings", "cline_mcp_settings.json");
const clineExtensionConfigPath = join(vscodePath, "globalStorage", "saoudrizwan.claude-dev", "settings", "cline_mcp_settings.json");
const copilotConfigPath = join(home, ".copilot", "mcp-config.json");
const UNSUPPORTED_STDIO_MESSAGE = "This agent supports only remote MCP servers (HTTP/SSE). Stdio commands are not supported.";
const ALL_TRANSPORTS = [
	"stdio",
	"http",
	"sse"
];
const mcpAgents = {
	antigravity: {
		name: "antigravity",
		displayName: "Antigravity",
		globalConfigPath: antigravityConfigPath,
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(home, ".gemini", "antigravity"))
	},
	cline: {
		name: "cline",
		displayName: "Cline (VSCode extension)",
		globalConfigPath: clineExtensionConfigPath,
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(clineExtensionConfigPath)
	},
	"cline-cli": {
		name: "cline-cli",
		displayName: "Cline CLI",
		globalConfigPath: clineCliConfigPath,
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(home, ".cline"))
	},
	"claude-code": {
		name: "claude-code",
		displayName: "Claude Code",
		globalConfigPath: join(home, ".claude.json"),
		projectConfigPath: ".mcp.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(home, ".claude.json")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".mcp.json"))
	},
	"claude-desktop": {
		name: "claude-desktop",
		displayName: "Claude Desktop",
		globalConfigPath: join(appSupport, "Claude", "claude_desktop_config.json"),
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ["stdio"],
		unsupportedTransportMessage: "Claude Desktop currently supports only stdio MCP servers. Use a package name or command instead of a URL.",
		detectGlobalInstall: () => existsSync(join(appSupport, "Claude", "claude_desktop_config.json"))
	},
	codex: {
		name: "codex",
		displayName: "Codex",
		globalConfigPath: join(process.env.CODEX_HOME?.trim() || join(home, ".codex"), "config.toml"),
		projectConfigPath: ".codex/config.toml",
		configKey: "mcp_servers",
		format: "toml",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(process.env.CODEX_HOME?.trim() || join(home, ".codex")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".codex", "config.toml")),
		transformConfig: (_name, config) => transformCodexServerConfig(config)
	},
	cursor: {
		name: "cursor",
		displayName: "Cursor",
		globalConfigPath: join(home, ".cursor", "mcp.json"),
		projectConfigPath: ".cursor/mcp.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(home, ".cursor")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".cursor", "mcp.json"))
	},
	"gemini-cli": {
		name: "gemini-cli",
		displayName: "Gemini CLI",
		globalConfigPath: join(home, ".gemini", "settings.json"),
		projectConfigPath: ".gemini/settings.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(home, ".gemini")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".gemini", "settings.json"))
	},
	goose: {
		name: "goose",
		displayName: "Goose",
		globalConfigPath: gooseConfigPath,
		projectConfigPath: ".goose/config.yaml",
		configKey: "extensions",
		format: "yaml",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(gooseConfigPath),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".goose", "config.yaml")),
		transformConfig: (name, config) => transformGooseServerConfig(name, config)
	},
	"github-copilot-cli": {
		name: "github-copilot-cli",
		displayName: "GitHub Copilot CLI",
		globalConfigPath: copilotConfigPath,
		projectConfigPath: ".vscode/mcp.json",
		configKey: "mcpServers",
		projectConfigKey: "servers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(copilotConfigPath),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".vscode", "mcp.json")),
		transformConfig: (_name, config, context) => context.global ? config : transformVscodeServerConfig(config)
	},
	mcporter: {
		name: "mcporter",
		displayName: "MCPorter",
		globalConfigPath: join(home, ".mcporter", "mcporter.json"),
		projectConfigPath: "config/mcporter.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(home, ".mcporter")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, "config", "mcporter.json"))
	},
	opencode: {
		name: "opencode",
		displayName: "OpenCode",
		globalConfigPath: join(process.env.XDG_CONFIG_HOME || join(home, ".config"), "opencode", "opencode.json"),
		projectConfigPath: "opencode.json",
		configKey: "mcp",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(process.env.XDG_CONFIG_HOME || join(home, ".config"), "opencode")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, "opencode.json")),
		transformConfig: (_name, config) => transformOpenCodeServerConfig(config)
	},
	vscode: {
		name: "vscode",
		displayName: "VS Code",
		globalConfigPath: join(vscodePath, "mcp.json"),
		projectConfigPath: ".vscode/mcp.json",
		configKey: "servers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => existsSync(join(vscodePath, "mcp.json")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".vscode", "mcp.json")),
		transformConfig: (_name, config) => transformVscodeServerConfig(config)
	},
	zed: {
		name: "zed",
		displayName: "Zed",
		globalConfigPath: join(appSupport, "Zed", "settings.json"),
		projectConfigPath: ".zed/settings.json",
		configKey: "context_servers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		unsupportedTransportMessage: UNSUPPORTED_STDIO_MESSAGE,
		detectGlobalInstall: () => existsSync(join(appSupport, "Zed")),
		detectProjectInstall: (cwd) => existsSync(join(cwd, ".zed", "settings.json")),
		transformConfig: (_name, config) => transformZedServerConfig(config)
	}
};
const mcpAgentAliases = {
	"cline-vscode": "cline",
	gemini: "gemini-cli",
	"github-copilot": "vscode"
};
const getMcpAgentConfig = (agentType) => mcpAgents[agentType];
const getMcpAgentTypes = () => Object.values(mcpAgents).map((config) => config.name);
const isMcpAgentType = (value) => value in mcpAgents;
const resolveMcpAgentAlias = (input) => {
	if (isMcpAgentType(input)) return input;
	return mcpAgentAliases[input] ?? null;
};
const isMcpTransportSupported = (agent, transport) => agent.supportedTransports.includes(transport);
const detectProjectInstalledMcpAgents = (cwd) => getMcpAgentTypes().filter((type) => mcpAgents[type].detectProjectInstall ? mcpAgents[type].detectProjectInstall(cwd) : false);
const detectGloballyInstalledMcpAgents = () => getMcpAgentTypes().filter((type) => mcpAgents[type].detectGlobalInstall());
const getMcpAgentsSupportingProjectScope = () => getMcpAgentTypes().filter((type) => Boolean(mcpAgents[type].projectConfigPath));
//#endregion
//#region src/mcp/build-server-config.ts
const buildMcpServerConfig = (parsed, options = {}) => {
	if (parsed.type === "remote") {
		const config = {
			type: options.transport ?? "http",
			url: parsed.value
		};
		if (options.headers && Object.keys(options.headers).length > 0) config.headers = options.headers;
		return config;
	}
	if (parsed.type === "command") {
		const parts = parsed.value.split(/\s+/);
		const config = {
			command: parts[0] ?? "",
			args: parts.slice(1)
		};
		if (options.env && Object.keys(options.env).length > 0) config.env = options.env;
		return config;
	}
	const config = {
		command: "npx",
		args: ["-y", parsed.value]
	};
	if (options.env && Object.keys(options.env).length > 0) config.env = options.env;
	return config;
};
//#endregion
//#region src/utils/get-nested-value.ts
const getNestedValue = (source, dottedKey) => {
	if (!source) return void 0;
	const segments = dottedKey.split(".");
	let cursor = source;
	for (const segment of segments) {
		if (!isPlainObject(cursor)) return void 0;
		cursor = cursor[segment];
	}
	return cursor;
};
//#endregion
//#region src/utils/ensure-parent-dir.ts
const ensureParentDir = (filePath) => {
	const parentDir = dirname(filePath);
	if (!existsSync(parentDir)) mkdirSync(parentDir, { recursive: true });
};
//#endregion
//#region src/utils/set-nested-value.ts
const DANGEROUS_KEY_SEGMENTS$1 = new Set([
	"__proto__",
	"prototype",
	"constructor"
]);
const assertSafeSegment = (segment) => {
	if (DANGEROUS_KEY_SEGMENTS$1.has(segment)) throw new Error(`Refusing to write to unsafe key segment "${segment}"`);
};
const setNestedValue = (target, dottedKey, value) => {
	const segments = dottedKey.split(".");
	let cursor = target;
	for (let segmentIndex = 0; segmentIndex < segments.length - 1; segmentIndex += 1) {
		const segment = segments[segmentIndex];
		assertSafeSegment(segment);
		const existing = cursor[segment];
		if (isPlainObject(existing)) {
			cursor = existing;
			continue;
		}
		const next = {};
		cursor[segment] = next;
		cursor = next;
	}
	const finalSegment = segments[segments.length - 1];
	assertSafeSegment(finalSegment);
	cursor[finalSegment] = value;
};
//#endregion
//#region src/utils/walk-nested-object.ts
const walkNestedObject = (root, segments) => {
	let cursor = root;
	for (const segment of segments) {
		if (!isPlainObject(cursor)) return void 0;
		cursor = cursor[segment];
	}
	return isPlainObject(cursor) ? cursor : void 0;
};
//#endregion
//#region src/mcp/formats/json.ts
const JSONC_FORMATTING = {
	insertSpaces: true,
	tabSize: 2,
	eol: "\n"
};
const readFileOrEmpty = (filePath) => existsSync(filePath) ? readFileSync(filePath, "utf-8") : "";
const writeWithTrailingNewline = (filePath, contents) => {
	writeFileSync(filePath, contents.endsWith("\n") ? contents : `${contents}\n`, "utf-8");
};
const readJsoncConfig = (filePath) => {
	const raw = readFileOrEmpty(filePath);
	if (!raw.trim()) return {};
	const parsed = parse$1(raw);
	return isPlainObject(parsed) ? parsed : {};
};
const setJsoncNestedValue = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existingText = readFileOrEmpty(filePath);
	const sourceText = existingText.trim() ? existingText : "{}";
	writeWithTrailingNewline(filePath, applyEdits(sourceText, modify(sourceText, [...dottedKey.split("."), serverName], serverConfig, { formattingOptions: JSONC_FORMATTING })));
};
const writeJsonConfigAtKey = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existing = readJsoncConfig(filePath);
	const existingServers = walkNestedObject(existing, dottedKey.split("."));
	const servers = existingServers ? { ...existingServers } : {};
	servers[serverName] = serverConfig;
	setNestedValue(existing, dottedKey, servers);
	writeFileSync(filePath, `${JSON.stringify(existing, null, 2)}\n`, "utf-8");
};
const removeJsoncConfigKey = (filePath, dottedKey, serverName) => {
	if (!existsSync(filePath)) return false;
	const sourceText = readFileSync(filePath, "utf-8");
	if (!sourceText.trim()) return false;
	const existing = readJsoncConfig(filePath);
	const segments = dottedKey.split(".");
	const parentObject = walkNestedObject(existing, segments);
	if (!parentObject || !(serverName in parentObject)) return false;
	const edits = modify(sourceText, [...segments, serverName], void 0, { formattingOptions: JSONC_FORMATTING });
	if (edits.length === 0) return false;
	writeWithTrailingNewline(filePath, applyEdits(sourceText, edits));
	return true;
};
//#endregion
//#region src/utils/delete-nested-value.ts
const DANGEROUS_KEY_SEGMENTS = new Set([
	"__proto__",
	"prototype",
	"constructor"
]);
const deleteNestedValue = (target, dottedKey) => {
	if (!target) return false;
	const segments = dottedKey.split(".");
	if (segments.some((segment) => DANGEROUS_KEY_SEGMENTS.has(segment))) return false;
	let cursor = target;
	for (let segmentIndex = 0; segmentIndex < segments.length - 1; segmentIndex += 1) {
		const segment = segments[segmentIndex];
		const existing = cursor[segment];
		if (!isPlainObject(existing)) return false;
		cursor = existing;
	}
	const lastSegment = segments[segments.length - 1];
	if (!(lastSegment in cursor)) return false;
	delete cursor[lastSegment];
	return true;
};
//#endregion
//#region src/mcp/formats/toml.ts
const toTomlJsonMap = (value) => JSON.parse(JSON.stringify(value));
const readTomlConfig = (filePath) => {
	if (!existsSync(filePath)) return {};
	const raw = readFileSync(filePath, "utf-8");
	if (!raw.trim()) return {};
	const parsed = TOML.parse(raw);
	return isPlainObject(parsed) ? parsed : {};
};
const writeTomlConfigAtKey = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existing = readTomlConfig(filePath);
	const existingServers = walkNestedObject(existing, dottedKey.split("."));
	const servers = existingServers ? { ...existingServers } : {};
	servers[serverName] = serverConfig;
	setNestedValue(existing, dottedKey, servers);
	writeFileSync(filePath, TOML.stringify(toTomlJsonMap(existing)), "utf-8");
};
const removeTomlConfigKey = (filePath, dottedKey, serverName) => {
	if (!existsSync(filePath)) return false;
	const existing = readTomlConfig(filePath);
	const didRemove = deleteNestedValue(existing, `${dottedKey}.${serverName}`);
	if (didRemove) writeFileSync(filePath, TOML.stringify(toTomlJsonMap(existing)), "utf-8");
	return didRemove;
};
//#endregion
//#region src/mcp/formats/yaml.ts
const readYamlConfig = (filePath) => {
	if (!existsSync(filePath)) return {};
	const raw = readFileSync(filePath, "utf-8");
	if (!raw.trim()) return {};
	const parsed = parse(raw);
	return isPlainObject(parsed) ? parsed : {};
};
const writeYamlConfigAtKey = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existing = readYamlConfig(filePath);
	const existingServers = walkNestedObject(existing, dottedKey.split("."));
	const servers = existingServers ? { ...existingServers } : {};
	servers[serverName] = serverConfig;
	setNestedValue(existing, dottedKey, servers);
	writeFileSync(filePath, stringify(existing), "utf-8");
};
const removeYamlConfigKey = (filePath, dottedKey, serverName) => {
	if (!existsSync(filePath)) return false;
	const existing = readYamlConfig(filePath);
	const didRemove = deleteNestedValue(existing, `${dottedKey}.${serverName}`);
	if (didRemove) writeFileSync(filePath, stringify(existing), "utf-8");
	return didRemove;
};
//#endregion
//#region src/mcp/formats/index.ts
const readConfigFile = (filePath, format) => {
	switch (format) {
		case "json":
		case "jsonc": return readJsoncConfig(filePath);
		case "yaml": return readYamlConfig(filePath);
		case "toml": return readTomlConfig(filePath);
		default: throw new Error(`Unsupported config format: ${format}`);
	}
};
const writeServerToConfigFile = (filePath, format, dottedKey, serverName, serverConfig) => {
	switch (format) {
		case "jsonc":
			setJsoncNestedValue(filePath, dottedKey, serverName, serverConfig);
			return;
		case "json":
			writeJsonConfigAtKey(filePath, dottedKey, serverName, serverConfig);
			return;
		case "yaml":
			writeYamlConfigAtKey(filePath, dottedKey, serverName, serverConfig);
			return;
		case "toml":
			writeTomlConfigAtKey(filePath, dottedKey, serverName, serverConfig);
			return;
		default: throw new Error(`Unsupported config format: ${format}`);
	}
};
const removeServerFromConfigFile = (filePath, format, dottedKey, serverName) => {
	switch (format) {
		case "json":
		case "jsonc": return removeJsoncConfigKey(filePath, dottedKey, serverName);
		case "yaml": return removeYamlConfigKey(filePath, dottedKey, serverName);
		case "toml": return removeTomlConfigKey(filePath, dottedKey, serverName);
		default: throw new Error(`Unsupported config format: ${format}`);
	}
};
const listServersInConfigFile = (filePath, format, dottedKey) => {
	const entries = getNestedValue(readConfigFile(filePath, format), dottedKey);
	return isPlainObject(entries) ? entries : {};
};
//#endregion
//#region src/mcp/resolve-config-target.ts
const resolveMcpConfigTarget = (agent, options = {}) => {
	const isGlobal = options.global ?? false;
	const cwd = options.cwd ?? process.cwd();
	return {
		configPath: agent.resolveConfigPath ? agent.resolveConfigPath({
			global: isGlobal,
			cwd
		}) : !isGlobal && agent.projectConfigPath ? join(cwd, agent.projectConfigPath) : agent.globalConfigPath,
		configKey: !isGlobal && agent.projectConfigKey ? agent.projectConfigKey : agent.configKey
	};
};
//#endregion
//#region src/mcp/installer.ts
const installMcpServerForAgent = (serverName, serverConfig, agentType, options = {}) => {
	const agent = getMcpAgentConfig(agentType);
	const { configPath, configKey } = resolveMcpConfigTarget(agent, options);
	const isGlobal = options.global ?? false;
	try {
		const transformed = agent.transformConfig ? agent.transformConfig(serverName, serverConfig, { global: isGlobal }) : serverConfig;
		writeServerToConfigFile(configPath, agent.format, configKey, serverName, transformed);
		return {
			agent: agentType,
			success: true,
			path: configPath
		};
	} catch (error) {
		return {
			agent: agentType,
			success: false,
			path: configPath,
			error: toErrorMessage(error)
		};
	}
};
const installMcpServerForAgents = (serverName, serverConfig, agentTypes, options = {}) => agentTypes.map((agentType) => installMcpServerForAgent(serverName, serverConfig, agentType, options));
//#endregion
//#region src/mcp/source-parser.ts
const REMOTE_URL_REGEX = /^https?:\/\//i;
const HAS_WHITESPACE_REGEX = /\s/;
const PACKAGE_NAME_REGEX = /^(?:@[a-z0-9-~][a-z0-9-._~]*\/)?[a-z0-9-~][a-z0-9-._~]*(?:@[^\s]+)?$/;
const PATH_SEPARATOR_REGEX = /[/\\]/;
const stripVersionSuffix = (input) => {
	if (input.startsWith("@")) {
		const secondAtIndex = input.indexOf("@", 1);
		if (secondAtIndex > 0) return input.slice(0, secondAtIndex);
		return input;
	}
	const atIndex = input.lastIndexOf("@");
	if (atIndex > 0) return input.slice(0, atIndex);
	return input;
};
const stripScopePrefix = (input) => {
	if (!input.startsWith("@") || !input.includes("/")) return input;
	return input.split("/")[1] || input;
};
const stripPathPrefix = (input) => {
	if (!PATH_SEPARATOR_REGEX.test(input)) return input;
	const segments = input.split(PATH_SEPARATOR_REGEX);
	return segments[segments.length - 1] || input;
};
const extractPackageName = (input) => {
	let name = stripVersionSuffix(input);
	name = stripScopePrefix(name);
	name = stripPathPrefix(name);
	name = name.replace(SCRIPT_EXTENSION_REGEX, "");
	for (const prefix of PACKAGE_NAME_PREFIX_STRIP) if (name.startsWith(prefix)) {
		name = name.slice(prefix.length);
		break;
	}
	for (const suffix of PACKAGE_NAME_SUFFIX_STRIP) if (name.endsWith(suffix)) {
		name = name.slice(0, -suffix.length);
		break;
	}
	return name || "mcp-server";
};
const inferNameFromUrl = (input) => {
	try {
		const labels = new URL(input).hostname.split(".").filter((segment) => segment.length > 0);
		if (labels.length === 0) return MCP_DEFAULT_SERVER_NAME;
		const meaningfulLabels = labels.filter((label) => {
			const lower = label.toLowerCase();
			if (COMMON_TLD_LABELS.has(lower)) return false;
			if (GENERIC_HOST_PREFIXES.has(lower)) return false;
			return true;
		});
		if (meaningfulLabels.length > 0) return meaningfulLabels[0];
		if (labels.length >= 2) return labels[labels.length - 2];
		return labels[labels.length - 1] || "mcp-server";
	} catch {
		return MCP_DEFAULT_SERVER_NAME;
	}
};
const inferNameFromCommand = (command) => {
	const tokens = command.trim().split(/\s+/);
	const runnerBase = tokens[0]?.split(PATH_SEPARATOR_REGEX).pop() ?? "";
	const startIndex = KNOWN_COMMAND_RUNNERS.has(runnerBase) ? 1 : 0;
	for (let tokenIndex = startIndex; tokenIndex < tokens.length; tokenIndex += 1) {
		const token = tokens[tokenIndex];
		if (!token || token.startsWith("-")) continue;
		return extractPackageName(token);
	}
	const firstNonFlag = tokens.find((token) => !token.startsWith("-"));
	return firstNonFlag ? extractPackageName(firstNonFlag) : MCP_DEFAULT_SERVER_NAME;
};
const parseMcpSource = (input) => {
	const trimmed = input.trim();
	if (trimmed.length === 0) throw new Error("Invalid MCP source: input is empty. Expected a remote URL, an npm package, or a command line.");
	if (REMOTE_URL_REGEX.test(trimmed)) return {
		type: "remote",
		value: trimmed,
		inferredName: inferNameFromUrl(trimmed)
	};
	if (HAS_WHITESPACE_REGEX.test(trimmed)) return {
		type: "command",
		value: trimmed,
		inferredName: inferNameFromCommand(trimmed)
	};
	if (PACKAGE_NAME_REGEX.test(trimmed)) return {
		type: "package",
		value: trimmed,
		inferredName: extractPackageName(trimmed)
	};
	return {
		type: "command",
		value: trimmed,
		inferredName: inferNameFromCommand(trimmed)
	};
};
const isRemoteMcpSource = (parsed) => parsed.type === "remote";
//#endregion
//#region src/mcp/install-mcp-server.ts
const resolveMcpTargetAgents = (requested, isGlobal, cwd) => {
	if (requested && requested.length > 0) return {
		agents: requested,
		detected: false
	};
	return {
		agents: isGlobal ? detectGloballyInstalledMcpAgents() : detectProjectInstalledMcpAgents(cwd),
		detected: true
	};
};
const installMcpServer = (options) => {
	const parsed = parseMcpSource(options.source);
	const isGlobal = options.global ?? false;
	const cwd = options.cwd ?? process.cwd();
	const serverName = options.name ?? parsed.inferredName;
	const serverConfig = buildMcpServerConfig(parsed, {
		transport: options.transport,
		headers: options.headers,
		env: options.env
	});
	const requestedTransport = parsed.type === "remote" ? serverConfig.type ?? "http" : "stdio";
	const { agents: targetAgents } = resolveMcpTargetAgents(options.agents, isGlobal, cwd);
	return {
		serverName,
		config: serverConfig,
		results: targetAgents.map((agentType) => {
			const agent = getMcpAgentConfig(agentType);
			if (!isMcpTransportSupported(agent, requestedTransport)) return {
				agent: agentType,
				success: false,
				path: "",
				error: agent.unsupportedTransportMessage ?? `${agent.displayName} does not support ${requestedTransport} transport.`
			};
			return installMcpServerForAgent(serverName, serverConfig, agentType, {
				global: isGlobal,
				cwd
			});
		})
	};
};
//#endregion
//#region src/mcp/list.ts
const listInstalledMcpServers = (options = {}) => {
	const agentTypes = options.agents ?? getMcpAgentTypes();
	const collected = [];
	for (const agentType of agentTypes) {
		const agent = getMcpAgentConfig(agentType);
		const { configPath, configKey } = resolveMcpConfigTarget(agent, options);
		if (!existsSync(configPath)) continue;
		const entries = listServersInConfigFile(configPath, agent.format, configKey);
		for (const [serverName, rawConfig] of Object.entries(entries)) collected.push({
			serverName,
			agent: agentType,
			path: configPath,
			config: rawConfig
		});
	}
	return collected;
};
//#endregion
//#region src/mcp/remove.ts
const removeMcpServerFromAgent = (serverName, agentType, options = {}) => {
	const agent = getMcpAgentConfig(agentType);
	const { configPath, configKey } = resolveMcpConfigTarget(agent, options);
	if (!existsSync(configPath)) return {
		agent: agentType,
		path: configPath,
		removed: false
	};
	try {
		return {
			agent: agentType,
			path: configPath,
			removed: removeServerFromConfigFile(configPath, agent.format, configKey, serverName)
		};
	} catch (error) {
		return {
			agent: agentType,
			path: configPath,
			removed: false,
			error: toErrorMessage(error)
		};
	}
};
const removeMcpServer = (options) => {
	const agentTypes = options.agents ?? getMcpAgentTypes();
	const results = [];
	for (const agentType of agentTypes) {
		const result = removeMcpServerFromAgent(options.name, agentType, {
			global: options.global,
			cwd: options.cwd
		});
		if (result.removed || result.error) results.push(result);
	}
	return results;
};
//#endregion
//#region src/mcp/index.ts
var mcp_exports = /* @__PURE__ */ __exportAll({
	DEFAULT_REMOTE_TRANSPORT: () => DEFAULT_REMOTE_TRANSPORT,
	NPX_COMMAND: () => "npx",
	NPX_DASH_Y: () => "-y",
	add: () => installMcpServer,
	buildMcpServerConfig: () => buildMcpServerConfig,
	detectGloballyInstalledMcpAgents: () => detectGloballyInstalledMcpAgents,
	detectProjectInstalledMcpAgents: () => detectProjectInstalledMcpAgents,
	extractPackageName: () => extractPackageName,
	getMcpAgentConfig: () => getMcpAgentConfig,
	getMcpAgentTypes: () => getMcpAgentTypes,
	getMcpAgentsSupportingProjectScope: () => getMcpAgentsSupportingProjectScope,
	install: () => installMcpServer,
	installMcpServer: () => installMcpServer,
	installMcpServerForAgent: () => installMcpServerForAgent,
	installMcpServerForAgents: () => installMcpServerForAgents,
	isMcpAgentType: () => isMcpAgentType,
	isMcpTransportSupported: () => isMcpTransportSupported,
	isRemoteMcpSource: () => isRemoteMcpSource,
	list: () => listInstalledMcpServers,
	listInstalledMcpServers: () => listInstalledMcpServers,
	listServersInConfigFile: () => listServersInConfigFile,
	mcpAgentAliases: () => mcpAgentAliases,
	mcpAgents: () => mcpAgents,
	parseMcpSource: () => parseMcpSource,
	parseSource: () => parseMcpSource,
	readConfigFile: () => readConfigFile,
	remove: () => removeMcpServer,
	removeMcpServer: () => removeMcpServer,
	removeMcpServerFromAgent: () => removeMcpServerFromAgent,
	removeServerFromConfigFile: () => removeServerFromConfigFile,
	resolveMcpAgentAlias: () => resolveMcpAgentAlias,
	resolveMcpConfigTarget: () => resolveMcpConfigTarget,
	resolveMcpTargetAgents: () => resolveMcpTargetAgents,
	writeServerToConfigFile: () => writeServerToConfigFile
});
//#endregion
export { NPX_DASH_Y as A, isMcpAgentType as C, resolveMcpAgentAlias as D, mcpAgents as E, DEFAULT_REMOTE_TRANSPORT as O, getMcpAgentsSupportingProjectScope as S, mcpAgentAliases as T, buildMcpServerConfig as _, installMcpServer as a, getMcpAgentConfig as b, isRemoteMcpSource as c, installMcpServerForAgents as d, resolveMcpConfigTarget as f, writeServerToConfigFile as g, removeServerFromConfigFile as h, listInstalledMcpServers as i, NPX_COMMAND as k, parseMcpSource as l, readConfigFile as m, removeMcpServer as n, resolveMcpTargetAgents as o, listServersInConfigFile as p, removeMcpServerFromAgent as r, extractPackageName as s, mcp_exports as t, installMcpServerForAgent as u, detectGloballyInstalledMcpAgents as v, isMcpTransportSupported as w, getMcpAgentTypes as x, detectProjectInstalledMcpAgents as y };
