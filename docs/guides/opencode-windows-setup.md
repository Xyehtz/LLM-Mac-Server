# Setup OpenCode on Windows

To install OpenCode on your Windows computer you can do it with multiple options, you can check all the available options [here](https://opencode.ai/download). For this tutorial I will be using OpenCode Desktop, but the configuration process will be the same for the terminal version.

After installing press Win+R and go to `%USERPROFILE%\.config\opencode`, inside of it create a file called `opencode.json`. 

Inside this file enter the following.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama",
      "options": {
        "baseURL": "http://<YOUR_SERVER_IP>:11434/v1"
      },
      "models": {
        "<YOUR_MODEL>": {
          "name": "YOUR_MODEL NAME"
        }
      }
    }
  },
  "model": "ollama/<YOUR-MODEL>"
}
```

Open OpenCode and you should see the model you have on your Ollama server as the default model, you can start talking to your model now, remember that in the first message you will have to wait a few more seconds than usual as OpenCode also sends a lot of agent information along the way.

You're ready to go.