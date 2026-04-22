# LLM Server setup

This is a personal guide on how to set up an LLM Server and access the models from other devices using tools such as OpenCode and Zed.

> [!NOTE]
> If you encounter any errors or wrong information on this guide create an Issue and to take a look at it.

This repository is divided on multiple guides each one covering a different topic, keep in mind that this repository is created with the idea of having a Mac (Apple Silicon) as the device hosting the LLM Server.

Some of the available guides

- [Ollama installation and setup on MacOS](./docs/guides/ollama-installation-and-setup-mac.md)
- [Tailscale setup](./docs/guides/tailscale-setup.md)
- [Zed Completion](./docs/guides/zed-completion.md)
- [Setup OpenCode on Windows](./docs/guides/opencode-windows-setup.md)
- [Considerations for other operating systems](./docs/guides/os-considerations.md)

Check common questions on [FAQ](./FAQ.md)

## FAQ

1. Where are Ollama models located
	Ollama models are located on different places according to the OS
	- MacOS: `/Users/<YOURUSERNAME>/.ollama/models` or the equivalent `~/.ollama/models`
	- Linux: `/usr/share/ollama/.ollama/models`
	- Windows: `C:\Users\<YOURUSERNAME\.ollama\models` or the equivalent `%USERPROFILE\.ollama\models`
