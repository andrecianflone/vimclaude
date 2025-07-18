# VimClaude

A Vim plugin that automatically reloads buffers when they're modified by Claude (or any external tool) without prompting the user. Features intelligent detection of modification sources and advanced Claude process detection.

## Features

- **Automatic monitoring**: Checks all open buffers for external modifications every 500ms (configurable)
- **Advanced Claude detection**: Multi-method detection of Claude Code processes and their working directories
- **Smart source detection**: Recognizes Claude Code, Git operations, build tools, and other external modifications
- **Intelligent reloading**: Only reloads files that haven't been modified in Vim (no unsaved changes)
- **Cursor preservation**: Maintains cursor position and view after reload
- **Project-aware**: Detects Claude's target project directory using multiple heuristics
- **Cross-platform**: Works on macOS, Linux, and Windows with platform-specific optimizations
- **Configurable**: Adjustable check interval, notification settings, and detection methods
- **Informative notifications**: Shows which tool modified each file with emoji icons
- **Non-intrusive**: Works silently in the background with minimal performance impact
- **Debug tools**: Built-in debugging functions for troubleshooting detection issues

## Installation

### Manual Installation

1. Copy the plugin file to your Vim plugin directory:
   ```bash
   mkdir -p ~/.vim/plugin
   cp vimclaude.vim ~/.vim/plugin/
   ```

2. Restart Vim or source the plugin:
   ```vim
   :source ~/.vim/plugin/vimclaude.vim
   ```

### Using a Plugin Manager

#### Vim-Plug
```vim
Plug 'your-username/vimclaude'
```

#### Pathogen
```bash
git clone https://github.com/your-username/vimclaude.git ~/.vim/bundle/vimclaude
```

## Configuration

Add these to your `.vimrc` to customize behavior:

```vim
" Enable/disable the plugin (default: 1)
let g:vimclaude_enabled = 1

" Check interval in milliseconds (default: 500)
let g:vimclaude_check_interval = 1000

" Show notifications when files are reloaded (default: 1)
let g:vimclaude_notify = 1

" Enable smart source detection (default: 1)
let g:vimclaude_smart_detection = 1

" Show emoji icons in notifications (default: 1)
let g:vimclaude_show_icons = 1

" Command to launch Claude (default: 'claude')
let g:vimclaude_claude_command = 'claude'

" Disable default key mappings (default: not set)
let g:vimclaude_no_mappings = 1
```

## Commands

- `:VimClaudeStart` - Start monitoring
- `:VimClaudeStop` - Stop monitoring
- `:VimClaudeToggle` - Toggle monitoring on/off
- `:VimClaudeStatus` - Show current monitoring status
- `:VimClaudeLaunch` - Show session menu and launch Claude in a vertical terminal split
- `:VimClaudeDebug [filename]` - Debug detection for a specific file (uses current file if no argument)

## Key Mappings

Default mappings (can be disabled with `g:vimclaude_no_mappings = 1`):

- `<leader>vcr` - Toggle auto-reload
- `<leader>vcs` - Show status
- `<leader>vcl` - Show session menu and launch Claude terminal

## How It Works

1. **File Monitoring**: Every 500ms (configurable), checks modification times of all open buffers
2. **Smart Detection**: Only reloads files that:
   - Have been modified externally (newer mtime)
   - Are not currently modified in Vim (no unsaved changes)
3. **Advanced Source Identification**: Uses multiple detection methods:
   - **Claude Code**: Multi-method process detection including:
     - Process working directory analysis (pwdx, lsof)
     - Command line argument inspection
     - Recent file access patterns
     - Project indicator detection (package.json, .git, etc.)
     - Cross-platform compatibility (macOS, Linux, Windows)
   - **Git**: Detects active git operations in the repository
   - **Build Tools**: Recognizes webpack, gulp, grunt, make, cargo, go, etc.
   - **Other**: Generic external modification
4. **Seamless Reload**: Preserves cursor position and view, switches back to original buffer
5. **Smart Notifications**: Shows appropriate message with emoji icons and modification source

## Use Cases

- **Claude Code Integration**: Automatically refresh when Claude modifies files
- **Session Management**: Quick access to Claude sessions with continue/resume options
- **External Tools**: Any build system, code generator, or external editor
- **Team Development**: When teammates modify files you have open
- **Live Development**: Hot-reloading during development

## Troubleshooting

### Plugin not working
```vim
:VimClaudeStatus
```

### Check if plugin is loaded
```vim
:echo exists('g:vimclaude_loaded')
```

### Manual reload test
```vim
:VimClaudeToggle
```

### Debug detection issues
```vim
:VimClaudeDebug /path/to/file.ext
```

This command provides detailed information about:
- Claude process detection
- Working directory analysis
- Target directory detection methods
- Path comparison results
- Final detection decisions

### Common issues and solutions

**Claude detection not working:**
- Use `:VimClaudeDebug` to see detailed process information
- Check if Claude Code is running in the same directory tree
- Verify that the file is in Claude's working directory or a subdirectory

**Files not reloading:**
- Ensure files don't have unsaved changes in Vim
- Check that `g:vimclaude_enabled = 1`
- Verify the check interval isn't too long

**Cross-platform issues:**
- The plugin uses different methods for macOS and Linux
- Windows support relies on process tools being available

## Notification Examples

With smart detection enabled, you'll see different messages:

- 🤖 **Claude modified**: `app.py` (when Claude Code edits files)
- 🔀 **Git modified**: `README.md` (during git operations)
- 🔧 **Build tool modified**: `dist/main.js` (webpack, npm, etc.)
- 📝 **External modification**: `config.json` (other tools)

## Advanced Usage

### Custom Check Interval
```vim
" Check every 2 seconds
let g:vimclaude_check_interval = 2000
```

### Silent Mode
```vim
" Disable notifications
let g:vimclaude_notify = 0
```

### Disable Smart Detection
```vim
" Fall back to generic notifications
let g:vimclaude_smart_detection = 0
```

### Text-Only Notifications
```vim
" Disable emoji icons
let g:vimclaude_show_icons = 0
```

### Project-Specific Settings
```vim
" In your project's .vimrc
autocmd BufRead,BufNewFile /path/to/claude/project/* let g:vimclaude_check_interval = 200
```

### Debug Mode for Development
```vim
" Enable detailed debugging for specific files
:VimClaudeDebug %

" Debug a specific file
:VimClaudeDebug /path/to/project/file.py
```

### Session Management
When you run `:VimClaudeLaunch` or use `<leader>vcl`, a popup window will appear in the center of your screen with session options:

1. **Continue session** - Launches Claude with `--continue` flag to continue the most recent session
2. **Select session** - Launches Claude with `--resume` flag, allowing you to choose which session to resume
3. **New session** - Launches Claude without flags to start a fresh session

**Navigation:**
- Use **arrow keys** or **j/k** to move between options
- Press **Enter** or **Space** to select
- Press **Esc** or **q** to cancel
- Press **1**, **2**, or **3** to directly select an option

The popup window is centered and overlays on top of your current Vim session for easy access.

### Custom Key Mappings
```vim
" Disable default mappings
let g:vimclaude_no_mappings = 1

" Create your own mappings
nnoremap <leader>cr :VimClaudeToggle<CR>
nnoremap <leader>cs :VimClaudeStatus<CR>
nnoremap <leader>cl :VimClaudeLaunch<CR>
nnoremap <leader>cd :VimClaudeDebug<CR>
```

### Custom Claude Command
```vim
" Use a specific Claude executable path
let g:vimclaude_claude_command = '/opt/homebrew/bin/claude'

" Or use a different command entirely
let g:vimclaude_claude_command = 'claude-dev'
```

## Compatibility

- **Vim**: 8.0+ (requires timer support)
- **Neovim**: All versions
- **Platforms**: Linux, macOS, Windows
- **Terminal**: Vim 8.1+ or Neovim (required for `:VimClaudeLaunch` command)
- **Popup Windows**: Vim 8.2+ or Neovim (for centered popup menu, falls back to command line menu on older versions)

## Safety Features

- Never reloads files with unsaved changes
- Preserves cursor position and view
- Graceful handling of deleted/moved files
- No interference with normal Vim operations
- Automatic cleanup of stored file modification times
- Cross-platform process detection safeguards
- Robust error handling for system commands

## License

MIT License - Feel free to modify and distribute.
