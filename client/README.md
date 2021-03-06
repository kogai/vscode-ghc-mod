# vscode-ghc-mod
This exension exposes ghc-mod functionality to VS Code. It requires having ghc-mod installed. I installed v5.5.0 compiled by GHC 7.10.3 on Windows via `cabal` using [these instructions][ghc-mod-instructions] and it is working. Hasn't been tested on Linux or OSX.

Symbol support requires having fast-tags or hasktags installed. For this reason, symbols are disabled by default. Change the configuration to enable it.

**New** Stack Support: I've tested it with simple stack projects (one stack.yaml/.cabal file in the root). Simply set `"haskell.ghcMod.executablePath": "stack"` in workspace or user settings (experimental and subject to change). Open an [issue](https://www.github.com/hoovercj/vscode-ghc-mod/issues) to help me improve it.

## Features:
- `check`: Works best when configured to run "onSave" with autosave turned on. "onChange" is experimental and may cause problems with type, info, and "Peek/Go to definition" until a newer version of ghc-mod has improved support for map-file.
- `type` and `info`: Displayed when hovering the mouse over a symbol. See below for configuration.
- `Go to definition`: Use `ctrl+click`, `f12`, or right-click -> "Peek/Go to definition".
- `Insert type`: There is now a command to insert a type definition for a function on the line above. The cursor must be in the name of function so ghc-mod can find the type information for that symbol.
- `Go to symbol`: Use `ctrl+shift+O` or type `@` in the command palette to see symbols in document.
- `Show all symbols`: Use `ctrl+T` or type `#` in the command palette followed by a search string to search for symbols across the entire workspace.

For linting, please use the [haskell-linter extension][haskell-linter-extension].

## Issues:
If you're having problems, enable logging and open the `ghc-mod server` output channel. If you think it is a bug instead of an environment issue, search for the issue or open a new one on [github](https://github.com/hoovercj/vscode-ghc-mod/issues).

## Pictures
### Check
Example 1:  
![Check](images/check.png)  
Example 2:  
![Check2](images/check2.png)  

### Info
Example 1:  
![Info](images/info.png)  
Example 2:  
![Info2](images/info2.png)  

### Type
![Type](images/type.png)

### Go To Definition
![Definition](images/definition.png)

### Go To Symbol
![Definition](images/symbols1.png)

### Show All Symbols
![Definition](images/symbols2.png)


## Configuration:
The following options can be set in workspace or user preferences:
```json
"haskell.ghcMod.maxNumberOfProblems": {
    "type": "number",
    "default": 100,
    "maximum": 100,
    "description": "Controls the maximum number of problems reported."
},
"haskell.ghcMod.executablePath": {
    "type": "string",
    "default": "ghc-mod",
    "description": "The full path to the ghc-mod executable, or 'stack' to use 'stack exec ghc-mod'."
},
"haskell.ghcMod.onHover": {
    "type": "string",
    "enum": [
        "info",
        "type",
        "fallback",
        "none"
    ],
    "default": "fallback",
    "description": "Controls the onHover behavior. 'info' will display ghc-mod info, 'type' will display ghc-mod type, 'fallback' will try info and fallback to type,and 'none' will disable onHover tooltips."
},
"haskell.ghcMod.check": {
    "type": "string",
    "enum": [
        "onSave",
        "onChange",
        "off"
    ],
    "default": "onSave",
    "description": "Controls whether ghc-mod check is enabled or not and when it triggers. For 'onSave' is recommended."
},
"haskell.ghcMod.logLevel": {
    "type": "string",
    "enum": [
        "none",
        "log",
        "info",
        "warn",
        "error"
    ],
    "default": "error",
    "description": "Controls the verbosity of logging. Logs can be seen in an output channel called 'ghc-mod server' or the chrome dev tools."
},
"haskell.symbols.provider": {
    "type": "string",
    "enum": [
        "none",
        "fast-tags",
        "hasktags"
    ],
    "default": "none",
    "description": "Selects a symbol provider."
},
"haskell.symbols.executablePath": {
    "type": "string",
    "default": "",
    "description": "Leave blank to use the default command for the selected provider (i.e. 'fast-tags'), otherwise set the full path to the executable. Extension may behave unexpectedly if the symbol provider setting is for a different command than the path provided."
}
```

## Changelog
__1.2.0__
- Closed [#35](https://github.com/hoovercj/vscode-ghc-mod/issues/35): Added experimental support for stack projects. Setting `haskell.ghcMod.executablePath` to `stack` will now use `stack exec` to execute ghc-mod.

__1.1.1__
- Closed [#51](https://github.com/hoovercj/vscode-ghc-mod/issues/51): Non-actionable errors no longer use window.showErrorMessage. Errors are still shown in the output channel under "ghc-mod server".

__1.1.0__
- Closed [#41](https://github.com/hoovercj/vscode-ghc-mod/issues/41): Fixed crashes on certain symbols (-1, -n, etc.). Thanks to @danstiner for the fix.
- Closed [#43](https://github.com/hoovercj/vscode-ghc-mod/issues/43): Added ["Haskell Syntax Highlighting"](https://marketplace.visualstudio.com/items?itemName=justusadam.language-haskell) as an extension dependency.
- Closed [#40](https://github.com/hoovercj/vscode-ghc-mod/issues/40): Logging uses output channel
- Closed [#37](https://github.com/hoovercj/vscode-ghc-mod/issues/37): When ghc-mod cannot be started, error prompt contains information and actions to change the user or workspace settings.

__1.0.1__
- Fixed path to uri issue on non-windows systems.

__1.0.0__
- Added "Go to symbol"/"Show all symbols" support based on fast-tags or hasktags. Thanks to @archaeron for starting the feature.

__0.3.0__
- Added "insert type" command. Thanks to @theor for the pull request.

__0.2.5__
- Fixed a ghc-mod crash on longer sets of comment dashes such as `-----------`.

__0.2.4__
- Setting `"haskell.ghcMod.check": "onChange"` will cause the extension to use `map-file`. This removes the need to save the file but can cause problems in current ghc-mod (5.5.0) and lower, but will hopefully fixed in an upcoming released so I've prepared for it now.
- Fixed a ghc-mod crash on `->` and `--`.

__0.2.2__
- Fixed a ghc-mod crash bug on mac when word is empty string (thanks @slepher).

__0.2.0__
- __Breaking:__ Files must be saved (auto-save is your friend).
- Type and Info tooltips are now more colorful (treated as code).
- Go To Definition works.

[ghc-mod-instructions]: http://www.mew.org/~kazu/proj/ghc-mod/en/install.html
[haskell-linter-extension]: https://marketplace.visualstudio.com/items/hoovercj.haskell-linter
