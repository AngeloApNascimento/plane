const require_chunk = require("./chunk-CZWwpsFl.cjs");
const require_to_error_message = require("./to-error-message-DMTSr_Bl.cjs");
let node_fs = require("node:fs");
let node_os = require("node:os");
let node_path = require("node:path");
let yaml = require("yaml");
let jsonc_parser = require("jsonc-parser");
let _iarna_toml = require("@iarna/toml");
_iarna_toml = require_chunk.__toESM(_iarna_toml, 1);
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
const home = (0, node_os.homedir)();
const getPlatformPaths = () => {
	const currentPlatform = (0, node_os.platform)();
	if (currentPlatform === "win32") {
		const appData = process.env.APPDATA || (0, node_path.join)(home, "AppData", "Roaming");
		return {
			appSupport: appData,
			vscodePath: (0, node_path.join)(appData, "Code", "User"),
			gooseConfigPath: (0, node_path.join)(appData, "Block", "goose", "config", "config.yaml")
		};
	}
	if (currentPlatform === "darwin") return {
		appSupport: (0, node_path.join)(home, "Library", "Application Support"),
		vscodePath: (0, node_path.join)(home, "Library", "Application Support", "Code", "User"),
		gooseConfigPath: (0, node_path.join)(home, ".config", "goose", "config.yaml")
	};
	const configDir = process.env.XDG_CONFIG_HOME || (0, node_path.join)(home, ".config");
	return {
		appSupport: configDir,
		vscodePath: (0, node_path.join)(configDir, "Code", "User"),
		gooseConfigPath: (0, node_path.join)(configDir, "goose", "config.yaml")
	};
};
const { appSupport, vscodePath, gooseConfigPath } = getPlatformPaths();
const antigravityConfigPath = (0, node_path.join)(home, ".gemini", "antigravity", "mcp_config.json");
const clineCliConfigPath = (0, node_path.join)(process.env.CLINE_DIR || (0, node_path.join)(home, ".cline"), "data", "settings", "cline_mcp_settings.json");
const clineExtensionConfigPath = (0, node_path.join)(vscodePath, "globalStorage", "saoudrizwan.claude-dev", "settings", "cline_mcp_settings.json");
const copilotConfigPath = (0, node_path.join)(home, ".copilot", "mcp-config.json");
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
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(home, ".gemini", "antigravity"))
	},
	cline: {
		name: "cline",
		displayName: "Cline (VSCode extension)",
		globalConfigPath: clineExtensionConfigPath,
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)(clineExtensionConfigPath)
	},
	"cline-cli": {
		name: "cline-cli",
		displayName: "Cline CLI",
		globalConfigPath: clineCliConfigPath,
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(home, ".cline"))
	},
	"claude-code": {
		name: "claude-code",
		displayName: "Claude Code",
		globalConfigPath: (0, node_path.join)(home, ".claude.json"),
		projectConfigPath: ".mcp.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(home, ".claude.json")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".mcp.json"))
	},
	"claude-desktop": {
		name: "claude-desktop",
		displayName: "Claude Desktop",
		globalConfigPath: (0, node_path.join)(appSupport, "Claude", "claude_desktop_config.json"),
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ["stdio"],
		unsupportedTransportMessage: "Claude Desktop currently supports only stdio MCP servers. Use a package name or command instead of a URL.",
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(appSupport, "Claude", "claude_desktop_config.json"))
	},
	codex: {
		name: "codex",
		displayName: "Codex",
		globalConfigPath: (0, node_path.join)(process.env.CODEX_HOME?.trim() || (0, node_path.join)(home, ".codex"), "config.toml"),
		projectConfigPath: ".codex/config.toml",
		configKey: "mcp_servers",
		format: "toml",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)(process.env.CODEX_HOME?.trim() || (0, node_path.join)(home, ".codex")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".codex", "config.toml")),
		transformConfig: (_name, config) => transformCodexServerConfig(config)
	},
	cursor: {
		name: "cursor",
		displayName: "Cursor",
		globalConfigPath: (0, node_path.join)(home, ".cursor", "mcp.json"),
		projectConfigPath: ".cursor/mcp.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(home, ".cursor")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".cursor", "mcp.json"))
	},
	"gemini-cli": {
		name: "gemini-cli",
		displayName: "Gemini CLI",
		globalConfigPath: (0, node_path.join)(home, ".gemini", "settings.json"),
		projectConfigPath: ".gemini/settings.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(home, ".gemini")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".gemini", "settings.json"))
	},
	goose: {
		name: "goose",
		displayName: "Goose",
		globalConfigPath: gooseConfigPath,
		projectConfigPath: ".goose/config.yaml",
		configKey: "extensions",
		format: "yaml",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)(gooseConfigPath),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".goose", "config.yaml")),
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
		detectGlobalInstall: () => (0, node_fs.existsSync)(copilotConfigPath),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".vscode", "mcp.json")),
		transformConfig: (_name, config, context) => context.global ? config : transformVscodeServerConfig(config)
	},
	mcporter: {
		name: "mcporter",
		displayName: "MCPorter",
		globalConfigPath: (0, node_path.join)(home, ".mcporter", "mcporter.json"),
		projectConfigPath: "config/mcporter.json",
		configKey: "mcpServers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(home, ".mcporter")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, "config", "mcporter.json"))
	},
	opencode: {
		name: "opencode",
		displayName: "OpenCode",
		globalConfigPath: (0, node_path.join)(process.env.XDG_CONFIG_HOME || (0, node_path.join)(home, ".config"), "opencode", "opencode.json"),
		projectConfigPath: "opencode.json",
		configKey: "mcp",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(process.env.XDG_CONFIG_HOME || (0, node_path.join)(home, ".config"), "opencode")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, "opencode.json")),
		transformConfig: (_name, config) => transformOpenCodeServerConfig(config)
	},
	vscode: {
		name: "vscode",
		displayName: "VS Code",
		globalConfigPath: (0, node_path.join)(vscodePath, "mcp.json"),
		projectConfigPath: ".vscode/mcp.json",
		configKey: "servers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(vscodePath, "mcp.json")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".vscode", "mcp.json")),
		transformConfig: (_name, config) => transformVscodeServerConfig(config)
	},
	zed: {
		name: "zed",
		displayName: "Zed",
		globalConfigPath: (0, node_path.join)(appSupport, "Zed", "settings.json"),
		projectConfigPath: ".zed/settings.json",
		configKey: "context_servers",
		format: "jsonc",
		supportedTransports: ALL_TRANSPORTS,
		unsupportedTransportMessage: UNSUPPORTED_STDIO_MESSAGE,
		detectGlobalInstall: () => (0, node_fs.existsSync)((0, node_path.join)(appSupport, "Zed")),
		detectProjectInstall: (cwd) => (0, node_fs.existsSync)((0, node_path.join)(cwd, ".zed", "settings.json")),
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
		if (!require_to_error_message.isPlainObject(cursor)) return void 0;
		cursor = cursor[segment];
	}
	return cursor;
};
//#endregion
//#region src/utils/ensure-parent-dir.ts
const ensureParentDir = (filePath) => {
	const parentDir = (0, node_path.dirname)(filePath);
	if (!(0, node_fs.existsSync)(parentDir)) (0, node_fs.mkdirSync)(parentDir, { recursive: true });
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
		if (require_to_error_message.isPlainObject(existing)) {
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
		if (!require_to_error_message.isPlainObject(cursor)) return void 0;
		cursor = cursor[segment];
	}
	return require_to_error_message.isPlainObject(cursor) ? cursor : void 0;
};
//#endregion
//#region src/mcp/formats/json.ts
const JSONC_FORMATTING = {
	insertSpaces: true,
	tabSize: 2,
	eol: "\n"
};
const readFileOrEmpty = (filePath) => (0, node_fs.existsSync)(filePath) ? (0, node_fs.readFileSync)(filePath, "utf-8") : "";
const writeWithTrailingNewline = (filePath, contents) => {
	(0, node_fs.writeFileSync)(filePath, contents.endsWith("\n") ? contents : `${contents}\n`, "utf-8");
};
const readJsoncConfig = (filePath) => {
	const raw = readFileOrEmpty(filePath);
	if (!raw.trim()) return {};
	const parsed = (0, jsonc_parser.parse)(raw);
	return require_to_error_message.isPlainObject(parsed) ? parsed : {};
};
const setJsoncNestedValue = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existingText = readFileOrEmpty(filePath);
	const sourceText = existingText.trim() ? existingText : "{}";
	writeWithTrailingNewline(filePath, (0, jsonc_parser.applyEdits)(sourceText, (0, jsonc_parser.modify)(sourceText, [...dottedKey.split("."), serverName], serverConfig, { formattingOptions: JSONC_FORMATTING })));
};
const writeJsonConfigAtKey = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existing = readJsoncConfig(filePath);
	const existingServers = walkNestedObject(existing, dottedKey.split("."));
	const servers = existingServers ? { ...existingServers } : {};
	servers[serverName] = serverConfig;
	setNestedValue(existing, dottedKey, servers);
	(0, node_fs.writeFileSync)(filePath, `${JSON.stringify(existing, null, 2)}\n`, "utf-8");
};
const removeJsoncConfigKey = (filePath, dottedKey, serverName) => {
	if (!(0, node_fs.existsSync)(filePath)) return false;
	const sourceText = (0, node_fs.readFileSync)(filePath, "utf-8");
	if (!sourceText.trim()) return false;
	const existing = readJsoncConfig(filePath);
	const segments = dottedKey.split(".");
	const parentObject = walkNestedObject(existing, segments);
	if (!parentObject || !(serverName in parentObject)) return false;
	const edits = (0, jsonc_parser.modify)(sourceText, [...segments, serverName], void 0, { formattingOptions: JSONC_FORMATTING });
	if (edits.length === 0) return false;
	writeWithTrailingNewline(filePath, (0, jsonc_parser.applyEdits)(sourceText, edits));
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
		if (!require_to_error_message.isPlainObject(existing)) return false;
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
	if (!(0, node_fs.existsSync)(filePath)) return {};
	const raw = (0, node_fs.readFileSync)(filePath, "utf-8");
	if (!raw.trim()) return {};
	const parsed = _iarna_toml.default.parse(raw);
	return require_to_error_message.isPlainObject(parsed) ? parsed : {};
};
const writeTomlConfigAtKey = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existing = readTomlConfig(filePath);
	const existingServers = walkNestedObject(existing, dottedKey.split("."));
	const servers = existingServers ? { ...existingServers } : {};
	servers[serverName] = serverConfig;
	setNestedValue(existing, dottedKey, servers);
	(0, node_fs.writeFileSync)(filePath, _iarna_toml.default.stringify(toTomlJsonMap(existing)), "utf-8");
};
const removeTomlConfigKey = (filePath, dottedKey, serverName) => {
	if (!(0, node_fs.existsSync)(filePath)) return false;
	const existing = readTomlConfig(filePath);
	const didRemove = deleteNestedValue(existing, `${dottedKey}.${serverName}`);
	if (didRemove) (0, node_fs.writeFileSync)(filePath, _iarna_toml.default.stringify(toTomlJsonMap(existing)), "utf-8");
	return didRemove;
};
//#endregion
//#region src/mcp/formats/yaml.ts
const readYamlConfig = (filePath) => {
	if (!(0, node_fs.existsSync)(filePath)) return {};
	const raw = (0, node_fs.readFileSync)(filePath, "utf-8");
	if (!raw.trim()) return {};
	const parsed = (0, yaml.parse)(raw);
	return require_to_error_message.isPlainObject(parsed) ? parsed : {};
};
const writeYamlConfigAtKey = (filePath, dottedKey, serverName, serverConfig) => {
	ensureParentDir(filePath);
	const existing = readYamlConfig(filePath);
	const existingServers = walkNestedObject(existing, dottedKey.split("."));
	const servers = existingServers ? { ...existingServers } : {};
	servers[serverName] = serverConfig;
	setNestedValue(existing, dottedKey, servers);
	(0, node_fs.writeFileSync)(filePath, (0, yaml.stringify)(existing), "utf-8");
};
const removeYamlConfigKey = (filePath, dottedKey, serverName) => {
	if (!(0, node_fs.existsSync)(filePath)) return false;
	const existing = readYamlConfig(filePath);
	const didRemove = deleteNestedValue(existing, `${dottedKey}.${serverName}`);
	if (didRemove) (0, node_fs.writeFileSync)(filePath, (0, yaml.stringify)(existing), "utf-8");
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
	return require_to_error_message.isPlainObject(entries) ? entries : {};
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
		}) : !isGlobal && agent.projectConfigPath ? (0, node_path.join)(cwd, agent.projectConfigPath) : agent.globalConfigPath,
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
			error: require_to_error_message.toErrorMessage(error)
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
		if (!(0, node_fs.existsSync)(configPath)) continue;
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
	if (!(0, node_fs.existsSync)(configPath)) return {
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
			error: require_to_error_message.toErrorMessage(error)
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
var mcp_exports = /* @__PURE__ */ require_chunk.__exportAll({
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
Object.defineProperty(exports, "DEFAULT_REMOTE_TRANSPORT", {
	enumerable: true,
	get: function() {
		return DEFAULT_REMOTE_TRANSPORT;
	}
});
Object.defineProperty(exports, "NPX_COMMAND", {
	enumerable: true,
	get: function() {
		return NPX_COMMAND;
	}
});
Object.defineProperty(exports, "NPX_DASH_Y", {
	enumerable: true,
	get: function() {
		return NPX_DASH_Y;
	}
});
Object.defineProperty(exports, "buildMcpServerConfig", {
	enumerable: true,
	get: function() {
		return buildMcpServerConfig;
	}
});
Object.defineProperty(exports, "detectGloballyInstalledMcpAgents", {
	enumerable: true,
	get: function() {
		return detectGloballyInstalledMcpAgents;
	}
});
Object.defineProperty(exports, "detectProjectInstalledMcpAgents", {
	enumerable: true,
	get: function() {
		return detectProjectInstalledMcpAgents;
	}
});
Object.defineProperty(exports, "extractPackageName", {
	enumerable: true,
	get: function() {
		return extractPackageName;
	}
});
Object.defineProperty(exports, "getMcpAgentConfig", {
	enumerable: true,
	get: function() {
		return getMcpAgentConfig;
	}
});
Object.defineProperty(exports, "getMcpAgentTypes", {
	enumerable: true,
	get: function() {
		return getMcpAgentTypes;
	}
});
Object.defineProperty(exports, "getMcpAgentsSupportingProjectScope", {
	enumerable: true,
	get: function() {
		return getMcpAgentsSupportingProjectScope;
	}
});
Object.defineProperty(exports, "installMcpServer", {
	enumerable: true,
	get: function() {
		return installMcpServer;
	}
});
Object.defineProperty(exports, "installMcpServerForAgent", {
	enumerable: true,
	get: function() {
		return installMcpServerForAgent;
	}
});
Object.defineProperty(exports, "installMcpServerForAgents", {
	enumerable: true,
	get: function() {
		return installMcpServerForAgents;
	}
});
Object.defineProperty(exports, "isMcpAgentType", {
	enumerable: true,
	get: function() {
		return isMcpAgentType;
	}
});
Object.defineProperty(exports, "isMcpTransportSupported", {
	enumerable: true,
	get: function() {
		return isMcpTransportSupported;
	}
});
Object.defineProperty(exports, "isRemoteMcpSource", {
	enumerable: true,
	get: function() {
		return isRemoteMcpSource;
	}
});
Object.defineProperty(exports, "listInstalledMcpServers", {
	enumerable: true,
	get: function() {
		return listInstalledMcpServers;
	}
});
Object.defineProperty(exports, "listServersInConfigFile", {
	enumerable: true,
	get: function() {
		return listServersInConfigFile;
	}
});
Object.defineProperty(exports, "mcpAgentAliases", {
	enumerable: true,
	get: function() {
		return mcpAgentAliases;
	}
});
Object.defineProperty(exports, "mcpAgents", {
	enumerable: true,
	get: function() {
		return mcpAgents;
	}
});
Object.defineProperty(exports, "mcp_exports", {
	enumerable: true,
	get: function() {
		return mcp_exports;
	}
});
Object.defineProperty(exports, "parseMcpSource", {
	enumerable: true,
	get: function() {
		return parseMcpSource;
	}
});
Object.defineProperty(exports, "readConfigFile", {
	enumerable: true,
	get: function() {
		return readConfigFile;
	}
});
Object.defineProperty(exports, "removeMcpServer", {
	enumerable: true,
	get: function() {
		return removeMcpServer;
	}
});
Object.defineProperty(exports, "removeMcpServerFromAgent", {
	enumerable: true,
	get: function() {
		return removeMcpServerFromAgent;
	}
});
Object.defineProperty(exports, "removeServerFromConfigFile", {
	enumerable: true,
	get: function() {
		return removeServerFromConfigFile;
	}
});
Object.defineProperty(exports, "resolveMcpAgentAlias", {
	enumerable: true,
	get: function() {
		return resolveMcpAgentAlias;
	}
});
Object.defineProperty(exports, "resolveMcpConfigTarget", {
	enumerable: true,
	get: function() {
		return resolveMcpConfigTarget;
	}
});
Object.defineProperty(exports, "resolveMcpTargetAgents", {
	enumerable: true,
	get: function() {
		return resolveMcpTargetAgents;
	}
});
Object.defineProperty(exports, "writeServerToConfigFile", {
	enumerable: true,
	get: function() {
		return writeServerToConfigFile;
	}
});
