//#region src/utils/is-plain-object.ts
const isPlainObject = (value) => value !== null && typeof value === "object" && !Array.isArray(value);
//#endregion
//#region src/utils/to-error-message.ts
const toErrorMessage = (error, fallback = "Unknown error") => error instanceof Error ? error.message : fallback;
//#endregion
Object.defineProperty(exports, "isPlainObject", {
	enumerable: true,
	get: function() {
		return isPlainObject;
	}
});
Object.defineProperty(exports, "toErrorMessage", {
	enumerable: true,
	get: function() {
		return toErrorMessage;
	}
});
