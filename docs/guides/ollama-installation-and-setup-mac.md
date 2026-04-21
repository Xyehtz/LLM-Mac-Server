# Ollama Installation and Setup (MacOS)
> [!NOTE]
> In this project I am using Mac as the main OS for the server, but other OS can be used for this make sure you take a look at [PLACEHOLDER CHANGE](PLACEHOLDER CHANGE)
> 
> Note that links and commands will be provided only for Mac on this section

## Installation
To install Ollama you can head to the website [Ollama Official website](https://ollama.com/download) or run the command below

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

After the installation is complete you should be able to open the Ollama application or type `ollama` on the terminal, after running the command you should see a selection between local models, Claude, Codex, and more.

## Downloading a model
This is a very important step and will most likely involve some trial and error in order to get the best possible model for your case, in my case I am very limited as I have a M2 Mac with only 8GB, therefore my best chances will be running 3B models or lower, or very limited 7B models. You can check all the available models on Ollama [here](https://ollama.com/library).

In my case I will be going with a model focused on coding that has a 3B version, in this case my best option is probably *Qwen2.5-Coder:3B*. To pull (download) the model you can run the following command.

```bash
ollama pull <MODEL_NAME>

# In my case
ollama pull qwen2.5-coder:3b
```

> [!NOTE]
> You may need to increase or specify the context window of the model, if you need to do this, take a look at [PLACEHOLDER CHANGE](PATH TO THE FILE)

## Setup the Ollama server to start on startup
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
COMMAND PID      USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME

ollama  537   YOU_USR   4u   IPv6 0xf3ef87e4402e166d      0t0  TCP *:11434 (LISTEN)
```