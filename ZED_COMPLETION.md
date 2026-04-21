# Setting up Code Completions on Zed
*Before you continue make sure you have completed the Ollama and Tailscale setup [here](./README.md)*

Setting up Code Completions on Zed is simple, again, make sure you have connection from your device to the server (i.e. your computer hosting Ollama).

Make sure you can access your server and specially Ollama.
```bash
curl http://<YOUR_TAILSCALE_IP>:11434

# You should receive "Ollama is running" as a reply
```

## Configure Zed
Open the `settings.json` file, you have different ways to do this.

1. Using the command palette (`ctrl+shift+p`) and then run `zed: open settings file`
2. Open the settings tab (`ctrl+,`) and at the top right select `Edit settings.json`
3. If you are on Linux go to `/home/<your-username>/.config/zed/settings.json`

Once in the file add the following.
```json
"language_models": {
  "ollama": {
    "api_url": "http://<YOUR_TAILSCALE_IP>:11434",
  },
},
"edit_predictions": {
  "ollama": {
    "api_url": "http://<YOUR_TAILSCALE_IP>:11434",
    "model": "<YOUR_SELECTED_MODEL>",
  },
  "provider": "ollama",
},
```
After this you should start seeing code completions in Zed.

![Image example of a code completion on Zed](./assets/images/zed_completion_example.png "Zed code completion example")