
### 4.3.4 Run Built-in Dialogue Demo

**Step 01** Run the Personal Assistant demo version.

Enter the following command in the terminal and press the `Enter` key:

```
src/examples/voice/assistant_grpc_demo.py
```

![](EPUB/images/image_184.jpg)

**Figure 4-86**

**Step 02** Grant Permission.

You will see a message prompting you to go to a URL to authenticate your application. This is because this program needs to use Google Assistant. To ensure the security of your Google account information, you need to grant permission to the URL displayed on the screen first, as shown in Figure 4-87.

![](EPUB/images/image_185.jpg)

**Figure 4-87**

Usually, the browser will automatically open this webpage. If not, you can copy and paste the link directly into your browser.

**Step 03** Confirm Account.

Log in to your account in the browser, then click the `ALLOW` button to connect your Kit to your Google account, as shown in Figure 4-88.

![](EPUB/images/image_186.jpg)

**Figure 4-88**

**Step 04** Run Demo.

Once connected, messages will appear sequentially in the Raspberry Pi terminal, then `Press button to start conversation...` will be displayed, as shown in Figure 4-89.

![](EPUB/images/image_187.jpg)

**Figure 4-89**

At this point, press the button and start speaking; do not speak for more than one minute.

**Step 05** Machine Learning.

Press the button once, then ask a question or give Google Assistant a command. For example, you can ask `what’s the weather today?` or say `sing me a song!` When you speak, the terminal will display the actions being performed, as shown in Figure 4-90.

![](EPUB/images/image_188.jpg)

**Figure 4-90**

Google Assistant may take several seconds to respond. If the Voice Kit cannot hear you clearly, you might be too close; try speaking from a bit further away.

**Step 06** Test Google Assistant (see Figure 4-91).

Here are some of our favorite things to say to Google Assistant:

```
"Beam me up Scotty!"
"How much wood could a woodchuck chuck..."
"I'm one of your engineers."
"How do you say 'this tastes delicious' in Korean?"
"How far away is the moon?"
"Beatbox!"
"Remind me to call my friend tomorrow morning."
```

![](EPUB/images/image_189.jpg)

**Figure 4-91**

**Step 07** Stop Demo Test.

When you finish testing, press the button again and say `Goodbye`, or simply press the `Ctrl+C` key combination to terminate the program.

When you press `Ctrl+C`, whether in the terminal or on Secure Shell Extension, it will terminate the command you are currently running. If you don't see the prompt, press `Ctrl+C` a few more times.

If it freezes, press `Ctrl+C`, then press the up arrow key to select the `src` command to restart.

**Step 08** Shut Down Demo.

When not using the Voice Kit, remember to shut it down and be careful not to damage the SD card.

Enter the following command, then press the `Enter` key:

```
sudo poweroff
```

You will see a message with R, C, and E options pop up on the screen (see Figure 4-92), and the green LED on the Raspberry Pi will turn off; at this point, you can unplug the power from the Kit. When you reconnect the power, remember to wait until the LED stops blinking before reconnecting to the Kit via SSH.

![](EPUB/images/image_190.jpg)

**Figure 4-92**

> **Note**
>
> If you close the Secure Shell Extension or unplug the power, the demo will stop working.
>
> To reconnect to your Kit, simply plug the power back in, wait 4 minutes for it to boot, and then you can reconnect via SSH.

> **Tip**
>
> If the IP address has changed, you can use the App to determine the new IP address first.

### 4.3.5 Set Demo to Auto-start on Boot

By default, your Voice Kit does nothing on startup; you must manually run a program like the Assistant demo. Creating a systemd service allows this project or other projects to run automatically on startup.

You only need a `.service` configuration file. This file can accept a long list of configuration options, so we provide a reference. You can find this file at `~/AIY-projects-python/src/examples/voice/assistant_grpc_demo.service`, with the following content:

![](EPUB/images/image_191.jpg)

Place this file in the `/lib/systemd/system/` directory, and it will work. You don't actually have to drag it to the above directory; you can create a symbolic link to this file. The specific operation is as follows:

![](EPUB/images/image_192.jpg)

Now make the system run this service on startup:

```
sudo systemctl enable assistant_grpc_demo.service
```

That's it for the setup! You can reboot to see if it works properly.

Or enter the following command to start it manually:

```
sudo service assistant_grpc_demo start
```

> **Note**
>
> Even if a prompt dialog appears, you still need to wait a moment for the program to start running. When the Assistant Demo is ready, the button will light up.

If you want to stop the service from running on startup, you can use the following command:

```
sudo systemctl disable assistant_grpc_demo.service
```

If it is currently running, you can stop it with the following command:

```
sudo service assistant_grpc_demo stop
```

You can check the status of this service with the following command:

```
sudo service assistant_grpc_demo status
```

You can copy the above configuration to a new file, e.g., `my_program.service`; the filename must end with `.service`. For most programs, you only need to change `ExecStart` to point to the Python file of the program. Then change `Description` to a description of the program. Then just repeat the above commands, replacing the filename with your filename.

If you want to learn more about service configuration files, you can click `.service config manual`.

### 4.3.6 View Logs

If you want to view more logs to help you troubleshoot or just out of curiosity, you can use the `journalctl` tool to view system logs and specific project logs.

By default, this tool outputs a lot of system information useless to you, so it's better to first set your program as a service, then tell `journalctl` to only print logs under this service.

For example, to run Assistant Demo as a service, you can enter the following command to output all logs for this service:

```
sudo journalctl -u assistant_grpc_demo -f
```

The `-f` option continuously outputs newly generated log information. Press `Ctrl+C` to stop the output.

### 4.3.7 Updates and Support Information

**Step 01** Download the latest `.img.xz` file from the Releases page on GitHub. For more release details, read the changelog.

**Step 02** Connect the MicroSD card to your computer using an adapter.

**Step 03** Use a program like Etcher to flash the `.img.xz` to your MicroSD card. (Etcher is free software and runs on Windows, macOS, and Linux.)

Once flashed, you can insert the MicroSD back into the Kit, and the project is complete!

Whether you are building a project for the first time or are very experienced, we hope this project inspires you.

## 4.4 More Ways to Play

Now, you have built your own smart speaker.

From the tests above, you should have a good idea of what the Voice Kit can do; now start exploring its functions freely.

In the content below, you can find instructions on Python APIs, hardware, and other program building tools.

You can use `#aiyprojects` to share your new creations with the maker community.

> **Note**
>
> The technical difficulty of the next section is much higher than before; if you are a programming beginner, you may need to work harder.

### 4.4.1 Python API Library

To better implement various Voice Kit functions, Google released a Python library. You can directly use the functions in the library without programming them yourself. This includes connecting to Google Assistant and Cloud Speech-to-Text, and using some Kit peripherals like buttons, LEDs, and other GPIO pins.

These APIs are packaged in a package called `aiy` and pre-installed in the Kit's system image. Make sure you have installed the latest system image.

You can read the API reference to learn more about the APIs. Here are some APIs you might use while using the Voice Kit:

*   `aiy.assistant`: Used to simplify connection operations with the Google Assistant API.
*   `aiy.cloudspeech`: Used to simplify connection with the Google Cloud Speech-to-Text service.
*   `aiy.voice.audio`: Used to record and play audio files.
*   `aiy.voice.tts`: Used to convert text to speech.
*   `aiy.board`: Used to control the button connected on the Voice Bonnet.
*   `aiy.leds`: Used to control the LED on the button.
*   `aiy.pins`: Used to define additional GPIO pins on the bonnet that might be used when using `gpiozero`.

#### Example: Voice Recorder

If you already have a demo as a template, learning the aiy Python API might be a bit easier.

These files are pre-stored in the `~/AIY-projects-python/src/examples/` path on your Kit. You can also find the source code for all examples on GitHub.

For example, you can run the `voice_recorder.py` file to further understand the `aiy.voice.audio` API.

```
cd ~/AIY-projects-python/src/examples/voice
./voice_recorder.py
```

At this point, if you press the button, the device will start recording what you say. After you finish speaking, release the button, and it will play back the recording you just made.

You can open this folder on the Raspberry Pi, or view its source code here, and then debug it.

If you are interested in hardware, such as how to use buttons and servo systems, you can read the content about GPIO pin expansion below, which contains relevant examples for reference.

### 4.4.2 Using Cloud Speech API

If you want to try another API, you can follow the guide below to experience the Google Cloud Speech-to-Text service. This feature converts your spoken commands into text, then uses that text to trigger your program. This API supports 80 languages and long audio clips, and can also perform fuzzy recognition.

To switch APIs, you first need to use the `src/examples/voice/cloudspeech_demo.py` code.

#### 1. Enable Billing

The Google Cloud Speech-to-Text service is a cloud platform service. If your usage is less than 60 minutes per month, this part is free; if it exceeds 60 minutes, you are charged at a rate of $0.006 per 15 seconds. The platform will send a warning message when you are about to exceed the free quota.

**Step 01** Open the navigation menu in the Google Cloud Console.

**Step 02** Click **Billing**.

**Step 03** If you don't have a billing account, click **Add billing account** and follow the instructions to complete the process.

**Step 04** Return to the Billing homepage, then click **My Projects**.

**Step 05** Find your new project name and ensure the project is linked to your billing account.

**Step 06** You can click the three-dot icon, then click **Change billing account** to link or change the project's billing account.

#### 2. Enable API

**Step 01** Open the navigation menu in the Console, then click **APIs & Services**.

**Step 02** Click **Enable APIs & Services**.

**Step 03** Enter **Cloud Speech API** in the search bar, then click the corresponding card.

**Step 04** Click **Enable** to turn on the API.

#### 3. Create Service Account and Credentials

**Step 01** Open the left navigation menu, click **APIs & Services**, then click **Credentials**.

**Step 02** Click **Create Credentials**, then click **Service account key** in the dropdown.

**Step 03** Select **New service account** from the **Service account** dropdown list.

**Step 04** Enter a service account name, e.g., **Voice Credentials** (the service account for your voice recognizer).

**Step 05** Select the **Viewer** role under **Project**.

**Step 06** Select **JSON** key type.

**Step 07** Click **Create**.

**Step 08** Your credentials will download automatically (filename consists of project name and some extra numbers); rename it to `cloud_speech.json`.

**Step 09** Open your workstation terminal, then enter the following command to move the `credentials.json` file to the corresponding folder.

```
(using the local file system)
```
