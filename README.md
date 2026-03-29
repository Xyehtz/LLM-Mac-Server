# Mac LLM Server setup

## Set up Ollama on Mac

Mac will be the server of the LLMs in this project, and therefore it will be the only place where Ollama will be installed and running

> [!NOTE]
> In this project I am using Mac as the main OS for the server, but other OS can be used for this make sure you take a look at [Considerations for other operating systems](#considerations-for-other-operating-systems)
> 
> Note that links and commands will be provided only for Mac on this section

### Install Ollama

To install Ollama you can head to the website [Ollama Official website](https://ollama.com/download) or run the command below

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

After the installation is complete you should be able to open the Ollama application or type `ollama` on the terminal, after running the command you should see a selection between local models, Claude, Codex, and more.

### Downloading a model

This is a very important step and will most likely involve some trial and error in order to get the best possible model for your case, in my case I am very limited as I have a M2 Mac with only 8GB, therefore my best chances will be running 3B models or lower, or very limited 7B models. You can check all the available models on Ollama [here](https://ollama.com/library).

In my case I will be going with a model focused on coding that has a 3B version, in this case my best option is probably *Qwen2.5-Coder:3B*. To pull (download) the model you can run the following command.

```bash
ollama pull <MODEL_NAME>

# In my case
ollama pull qwen2.5-coder:3b
```

### Modifying the context window

By default, Ollama uses a context window based on the available VRAM
- Less than 24 GB VRAM results in a 4K context window
- 24 to 48 GB VRAM results in a 32K context window
- 48 GB or more is 256K in context window

There are two ways to increase the context window of the model.

#### Modelfile

To increase the context window with a Modelfile you can do the following. Using a Modelfile you don't need to have the model installed previously as this process will also download it automatically

```bash
touch Modelfile
nano Modelfile
```

Inside the model file add the following

```bash
FROM <YOUR_MODEL>
PARAMETER num_ctx 32768 # I will be using a window of 32K tokens
```

Lastly run the following command to download the model with 32K Context using Ollama run

```bash
ollama create <YOUR_MODEL>-32k -f Modelfile
```

#### Modifying an already installed model

If you already have a model installed you can modify it to have 32k context doing the following

> [!NOTE]
> This will still create a new model on your computer

```bash
ollama run <YOUR_MODEL>
>>> /set parameter num_ctx 32768
>>> /save <YOUR_MODEL>-32k
>>> /bye
```

You should be able to check the available modes on your computer by using the following command

```bash
ollama list
```

You should see `<YOUR_MODEL>-32k` in the list 

### Starting an Ollama server on startup

Now that we have the Ollama models installed on our Mac, we need to find a way to start Ollama and serve Ollama on port 11434 on startup. To do this we can create a `plist` file and add it to the LaunchDaemons folder on our Mac, making it possible for the Ollama server to run immediately after log in.

Run the following.

```bash
touch ollama.plist
nano ollama.plist
```

Inside of `ollama.plist` enter the following

```bash
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>ollama</string>

    <key>UserName</key>
    <string>YOU_USERNAME</string>

    <key>EnvironmentVariables</key>
    <dict>
           <key>OLLAMA_HOST</key>
           <string>0.0.0.0:11434</string>
    </dict>

    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/ollama</string>
        <string>serve</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

After you have created the file run the following commands.

```bash
sudo cp ollama.plist /Library/LaunchDaemons
sudo chown root:admin /Library/LaunchDaemons/ollama.plist
```

If everything went well up to here you should be able to restart your computer and after logging you can run the following command to confirm that everything is working well.

```bash
lsof -i :11434
```

You should be able to see and output like this.

``` bash
COMMAND PID      USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME

ollama  537   YOU_USR   4u   IPv6 0xf3ef87e4402e166d      0t0  TCP *:11434 (LISTEN)
```

## Setup Tailscale on Windows and Mac

This process is divided on two versions, the Windows set up and the Mac setup.

### Setup Tailscale on Mac

For this process we will be using the Open Source version of Tailscale. To download it first make sure you have Golang installed on your Mac, you can install it [here](https://go.dev/dl/) or if you have Homebrew on your Mac you can run the following.

```bash
brew install go
```

After it has been installed run `go version` and you should see the version of Go that you have downloaded.

After that, run the following command to install the installation of Tailscale on your computer

```bash
go install tailscale.com/cmd/tailscale{,d}@main
```

With this Tailscale should be installed on your Mac, now run the daemon.

```bash
sudo $HOME/go/bin/tailscaled install-system-daemon
```

With this command Tailscale should start automatically after login, to confirm that the model worked properly, you can go to `/Library/LaunchDaemons` and you should see a `com.tailscale.tailscaled.plist` file in there

Finally, before finishing on Mac, you will have to run the following command to authenticate your Tailscale and be able to communicate between Windows and Mac.

```bash
~/go/bin/tailscale up
```

This command will show you a link that you need to follow to log in, after you do this Tailscale will run after you log in without the need to authenticate again.

### Install Tailscale on Windows

The installation on Windows is more simple, simply go to the [Tailscale website](https://tailscale.com/download) and install the application, it will also prompt to authenticate, make sure that you use the same account, after this is done you'll be ready to go.

If you want to run Tailscale on start up on your computer you can press Win+R and enter `shell:startup`, this will open a folder on Windows Explorer, here you can either drop or create a shortcut that points to the `.exe` file of Tailscale.

After you have set up Tailscale on both computers you should be able to see both devices active and the Tailscale IP Address for each one of them.

To make sure our computers can communicate between each other run the following command

On Windows.

```powershell
curl http://<YOUR_MAC_IP>:11434
```

You should receive `Ollama is running` on your shell, this means Windows is communicating with your Mac, and more importantly, Ollama is receiving requests.

On Mac you can ping the IP of your Windows computer and should all the responses.

## Setup OpenCode on Windows

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
        "baseURL": "http://<YOUR_MAC_TAILSCALE_IP>:11434/v1"
      },
      "models": {
        "<YOUR_MODEL>-32k": {
          "name": "YOUR_MODEL 32K"
        }
      }
    }
  },
  "model": "ollama/<YOUR-MODEL>-32K"
}
```


## Considerations for other operating systems

If you are going to use other Operating Systems keep in mind that the way models work with the hardware is slightly different.

In this project I am using Mac and the models will work slightly different from Linux and Windows.

Apple Silicon (Apple's M chips) computers have an architecture called *Unified Memory Architecture* (UMA) which provides a memory pool for the CPU, GPU and Neural Engine which is shared between the three, this means that on M chip computers you will be looking at the RAM and how big of a model can fit in the available RAM rather than looking at the VRAM like you would on Linux and Windows.

If you are going to follow the instructions of this project on Linux/Windows make sure to look at the VRAM when memory sizes are mentioned, not the RAM.

## FAQ

1. Where are Ollama models located
	Ollama models are located on different places according to the OS
	- MacOS: `/Users/<YOURUSERNAME>/.ollama/models` or the equivalent `~/.ollama/models`
	- Linux: `/usr/share/ollama/.ollama/models`
	- Windows: `C:\Users\<YOURUSERNAME\.ollama\models` or the equivalent `%USERPROFILE\.ollama\models`

