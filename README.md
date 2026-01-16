<div align="center">
  <img src="picocode.gif" width="600"/>
  <br/>
  <br/>
  <h1>⚡️ picocode</h1>
  <br/>
</div>



**picocode** is a minimal, Rust-based implementation similar to Claude Code. It's a small, robust tool designed for developers who want a lightweight coding assistant that is easy to reason about, safe to use, and highly hackable.

Rather than being a "do-it-all" platform, **picocode** focuses on being a reliable, strongly-typed bridge between your terminal and any LLM provider. Use it as a daily driver, or use it as a starting point for your own agent, you choose!

## 🦀 Why picocode?

- **Minimalist Implementation**: A clean, compact codebase written in Rust using the [Rig](https://github.com/0xPlayground/rig) library.
- **Interactive & Scriptable**: Use it as an interactive CLI, pipe it into scripts, or integrate it directly into your own Rust projects as a library.
- **Wide LLM Support**: Minimal footprint, but maximum reach. Works with Anthropic (Claude), OpenAI, DeepSeek, Google (Gemini), Ollama, and many more.
- **Safety First**: Destructive actions (like deleting files or running shell commands) require manual confirmation by default.
- **Robust & Typed**: Leverages Rust's type system to ensure tool calls and file operations are handled safely and predictably.

## 🚀 Usage

### Installation

```bash
cargo install --path .
```

Or download a release from [releases](http://github.com/jondot/picocode/releases)


### Interactive Mode
Launch a minimal interactive loop in your current workspace (this is the default):
```bash
picocode
```

### Scripting & Automation
Since it's a CLI tool, you can easily use it within your own shell scripts. For example, to automate a codebase-wide refactor:

```bash
# Ask picocode to refactor a file and get the output directly
picocode --input "Rewrite src/main.rs to use the anyhow crate" --yolo --bash
```

### Use as a Library
Picocode is structured as a library (`lib.rs`) and a binary (`main.rs`). You can add it as a dependency and use the same robust agents in your own Rust tools.

See [examples/basic_usage.rs](examples/basic_usage.rs) for a complete, runnable example.

```rust
use picocode::{create_agent, AgentConfig, ConsoleOutput};
use std::sync::Arc;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let output = Arc::new(ConsoleOutput::new());

    let agent = create_agent(AgentConfig {
        provider: "anthropic".into(),
        model: "claude-3-5-sonnet-latest".into(),
        output,
        use_bash: true,
        yolo: false,
        tool_call_limit: 10,
        system_message_extension: None,
    }).await?;

    let response = agent.run_once("Analyze the current project".into()).await?;
    println!("Response: {}", response);
    Ok(())
}
```

### Silent Mode (Library Usage)
If you want to use picocode as a library without any terminal output, use `NoOutput`:

```rust
use picocode::{create_agent, AgentConfig, NoOutput};
// ...
let output = Arc::new(NoOutput);
// ...
let response = agent.run_once("Your prompt".into()).await?;
```

## 🛡 Safety & Control

Picocode is designed to be a "tool in your hand," not a "ghost in your machine." 

- **Confirmation Loop**: By default, it asks before running `bash`, `remove`, or `write_file`.
- **YOLO Mode**: Use `--yolo` if you trust the agent and want it to work unattended in scripts.
- **Tool Limits**: Prevents "infinite loops" of AI thought with a default limit of 10 tool calls per turn.

## ⚙️ Configuration

Set your API keys as environment variables:
```bash
export ANTHROPIC_API_KEY=...
export OPENAI_API_KEY=...
```

Pick your model:
```bash
picocode -p anthropic -m claude-3-5-sonnet-latest
picocode -p deepseek -m deepseek-chat
```

## 🛠 Minimal Core, Maximum Power

While **picocode** is minimalist in its implementation, it provides a comprehensive suite of tools that gives the AI full control over a development environment:

- **Filesystem Mastery**: Atomic `edit_file` for precise refactoring, plus `read`, `write`, `move`, `copy`, and `remove` operations.
- **Deep Search**: Project-wide `grep` for text patterns and `glob` for finding files across complex directory structures.
- **Shell Integration**: Full `bash` access for running tests, building binaries, or managing infrastructure.
- **Web Capabilities**: Seamlessly integrates with `agent-browser` for live web search, documentation reading, or UI testing.
- **Extensible**: Designed to be hacked—easily add your own tools or tweak the agent's behavior in `tools.rs`.

---

Built for speed, safety, and simplicity. MIT Licensed.
