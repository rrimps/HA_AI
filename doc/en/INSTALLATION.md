
## INSTALLATION

> ⚠️ **Before continuing**, ensure everything is already working with the original Assist integration in Home Assistant and your instance of HA is exposed to the Internet with an valid domain and HTTPS certificate.

### Table of Contents

1. [Creating the Alexa Skill](#creating-the-alexa-skill)
2. [Obtaining the home_assistant_agent_id](#obtaining-the-home_assistant_agent_id-from-assist-or-the-generative-ai-if-you-are-using-one)
3. [Obtaining the home_assistant_token](#obtaining-the-home_assistant_token-long-lived-token)
4. [Setting the Invocation Name](#setting-the-invocation-name)
5. [Publishing the Skill](#publishing-the-skill)
6. [Enabling room recognition](#enabling-room-recognition-works-only-with-ai)
7. [Enabling conversation starter with prompt from Home Assistant](#enabling-conversation-starter-with-prompt-from-home-assistant)


### Creating the Alexa Skill

1. Create a Skill in the [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask) by following the steps below:
   - **Name your Skill**: Choose a name of your preference (e.g., Home Assistant Assist)
   - **Choose a primary locale**: Portuguese (BR)
   - **Choose a type of experience**: Other
   - **Choose a model**: Custom
   - **Hosting services**: Alexa-hosted (Python)
   - **Hosting region**: US East (N. Virginia) is the default, but you must use the same region where you created the AWS account and configured IAM [Instructions here](https://www.home-assistant.io/integrations/alexa.smart_home)
   - **Templates**: Click on `Import skill`
   - **Insert the address**: [https://github.com/fabianosan/HomeAssistantAssist.git](https://github.com/fabianosan/HomeAssistantAssist.git) and click `Import`
2. Go to the **Code** tab.
3. Insert your information into the configuration file as instructed below:
   - Open the `config.cfg` file in the project's root directory (/Skill Code/lambda/).
   - Insert the following information (required settings):
     ```txt
     home_assistant_url=https://YOUR-HOME-ASSISTANT-EXTERNAL-URL
     home_assistant_token=YOUR-HOME-ASSISTANT-TOKEN
     ```
   - **home_assistant_url**: External URL of your Home Assistant (root path).
   - **home_assistant_token**: Long-lived access token for your Home Assistant.

   - You can add these `optional` settings as well (but pay attention to the instructions each requires):
     ```txt
     home_assistant_agent_id=YOUR-AGENT-ID
     home_assistant_language=pt-BR
     home_assistant_room_recognition=False
     home_assistant_dashboard=YOUR-DASHBOARD-ID
     home_assistant_kioskmode=False
     ask_for_further_commands=False
     assist_input_entity=input_text.assistant_input
     debug=true
     ```
   - **(optional) home_assistant_agent_id**: Conversation agent ID configured in your Home Assistant; if not set, Assist will be used (Default).
   - **(optional) home_assistant_language**: Language to call the Home Assistant conversation API. If not set, the agent's default language will be used.
   - **(optional) home_assistant_room_recognition**: Enable device area recognition mode with `True`. **Attention**, it only works with AI and need extra setup in Home Assistant. _if you're using the default Assist, disable this option, as no command will work._
   - **(optional) home_assistant_dashboard**: Dashboard path to display on Echo Show, e.g., `mushroom`; if not set, "lovelace" will be loaded.
   - **(optional) home_assistant_kioskmode**: Enable kiosk mode with `True`. **Attention**, only activate this option if you have the [kiosk mode component](https://github.com/maykar/kiosk-mode) installed.
   - **(optional) ask_for_further_commands**, Enabling further commands with `True`. This variable determines whether Alexa will ask for further commands after responding. Set it to `True` to enable this behavior or `False` to disable it. The default is `False`.
   - **(optional) assist_input_entity**: Enable conversation starter with prompt from Home Assistant `input_text.assistant_input`. **Attention**, this feature require [extra setup in Home Assistant](#enabling-conversation-starter-with-prompt-from-home-assistant).
   - **(optional) debug**, Enable debbuging with `True`. Set this variable to log the debug messages.

4. If you wish, change the skill responses in the `/locale/en-US.lang` file or another supported language.
5. After making any changes in the code or configuration, click `Save`.
6. Then click `Deploy`.
7. Finally, go back to the `Build` tab and click `Build skill`.


### Obtaining the `home_assistant_agent_id` from Assist or the generative AI (if you are using one)

- Navigate to **Developer Tools**, go to the `Actions` tab, and follow the steps below: 
1. Search for `conversation.process` in the action field and select it:

  ![Action: Conversation: Process](images/dev_action.png)

2. Enable the `Agent` field and select the desired conversation agent from the list:

  ![Action: Agent](images/dev_action_uimode.png)

3. Switch to `YAML MODE` and copy the ID from the `agent_id` field:

  ![Action: Agent ID](images/dev_action_yaml.png)

### Obtaining the `home_assistant_token` (Long-Lived Token)

- With your Home Assistant open, go to your user profile in the bottom-left corner, click on it, and then go to the `Security` tab at the top:
  1. At the bottom of the page, click the `CREATE TOKEN` button:
  2. Enter a name that you find appropriate, e.g., `Home Assistant Skill Assist` and click `OK`:

    ![Create token](images/token.png)

  1. Copy the token:

    ![Token created](images/token_created.png)

  4. Place the generated token in the configuration

### Setting the ``Invocation Name``

- The default invocation name set in the code is "smart house."
- To change the invocation name:
  1. Go to the **Build** tab.
  2. Click on `Invocations` and then on `Skill Invocation Name`.
  3. Enter the desired new invocation name and save the changes (test if this wake word can be used in the **Test** tab).
  4. Rebuild the model by clicking on `Build skill` if you make changes.
  
### Publishing the Skill

1. After deploying the code in the **Code** tab, return to the **Build** tab and click on **Build skill**.
2. Then go to the **Alexa** app on your phone: `More` > `Skills & Games` > scroll to the bottom and click on `Your Skills` > `Dev.`, click on the skill you just created and **activate** it.

    ![Dev skills](images/alexa_dev_app.jpg)
    ![Activate for use](images/alexa_dev_app_activated.jpg)
3. Go back to the ``Alexa Developer Console`` and test the Skill in the **Test** tab to ensure the wake word and skill are working correctly.

### Enabling room recognition (works only with AI)
- In this mode, the skill sends the device ID (from the `Echo` device running the skill) in the Home Assistant conversation API call. Then, with a command instruction for the AI and a label associated with the device, the AI can identify the devices in the same area where your `Alexa` is located. To enable it, follow the steps below:

  ***Attention!***
  ## This mode makes commands slower and requires more complex configurations:
  1. Change the `home_assistant_room_recognition` setting to `True`, then redeploy and perform a new `Build Model` for the skill;
  2. Enable conversation API debug logging by adding the following configuration to Home Assistant's `configuration.yaml`:
  - Insert the following information:
     ```txt
     logger:
       logs:
         homeassistant.components.conversation: debug
     ```
  3. Restart Home Assistant and start the skill from the desired Echo device. After activation, the log will show the instruction received by the skill as in the example below:
    ```txt
    2024-10-10 11:04:56.798 DEBUG (MainThread) [homeassistant.components.conversation.agent_manager] Processing in en-US: turn on the living room light. device_id: amzn1.ask.device.AMAXXXXXX
     ```
     You can also obtain the device_id from the log "device: " in the ``Alexa Developer Console`` in ``Cloud Watch`` if you know how to do it.
  4. Take the entire identifier after the device_id, e.g., `amzn1.ask.device.AMAXXXXXX`, and add a new label to the **Echo device** using the `Alexa Media Player` Integration:
  
    ![Echo device label with device ID received from the skill](images/echo_device_label.png)
    
  5. Update your preferred **AI command prompt** with the following instruction:
     ```txt
     If asked to perform an action and no area is specified for the device, capture the identifier contained after "device_id:" in the command, obtain the label with the same identifier, and associate the device requested in the command to the label area found.
     ```

### Enabling conversation starter with prompt from Home Assistant

  #### This setup add prompter feature to enable Alexa conversations started from Home Assistant

  > ⚠️ **Importante:** Before continuing you must have [Alexa Media Player](https://github.com/alandtse/alexa_media_player) installed and configured.

  1. Enable the configuration in Alexa skill:
    - Add the following line to your `config.cfg` file:

      ```
      assist_input_entity = input_text.assistant_input
      ```

  2. Create a Text Helper in Home Assistant:
  
      1. Open Home Assistant.
      2. Go to: **Settings → Devices & Services → Helpers**
      3. Click **Create Helper** → Choose **Text**.
      4. Set the following options:
          - **Name:** `assistant_input`
          - **Maximum number of characters:** `255` (this is the hard limit)
      5. Click **Create**.

      > ⚠️ Note: 255 characters is a hard limitation for prompt size. There's no reliable workaround yet, except embedding other text inputs into the prompt.

  3. Create a Script in Home Assistant:

      1. Go to the [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask).
      2. On your skill’s home page, click **Copy Skill ID**.
      3. In Home Assistant, navigate to **Settings → Automations & Scenes → Scripts**.
      4. Click **Add Script** and enter a name like `Prompt Alexa Device`.
      5. Click the three-dot menu (⋮) and switch to **YAML mode**.
      6. Paste the following YAML into the editor.  
        Replace the placeholders:
          - `*your Skill ID*` → your actual Alexa skill ID  
          - `*the alexa you want to target*` → the `media_player` entity ID of your Alexa device

          ```
          sequence:
            - action: input_text.set_value
              metadata: {}
              data:
                value: "{{prompt}}"
              target:
                entity_id: input_text.assistant_input
            - action: media_player.play_media
              data:
                media_content_id: *your Skill ID*
                media_content_type: skill
              target:
                entity_id: *the alexa you want to target*
            - delay:
                hours: 0
                minutes: 0
                seconds: 10
                milliseconds: 0
            - action: input_text.set_value
              metadata: {}
              data:
                value: none
              target:
                entity_id: input_text.assistant_input 
          alias: prompt on Alexa device
          description: ""
          fields:
            prompt:
              selector:
                text: null
              name: prompt
              description: >-
                The prompt to pass to the skill, used as the first message to start a conversation.
              required: true
          ```

      7. Click **Save**.

  4. Call the Script from an Automation

      Now that the script is set up, you can trigger it from an automation. This will:
        - Pass a prompt to your Alexa skill;
        - Begin a spoken conversation using the assistant's response.
        ### Example Automation Action

        ```
        action: script.prompt_alexa_device
        metadata: {}
        data:
          prompt: >-
            Suggest that I remember to lock all doors and windows in the house before leaving
        ```

        Other sample prompts:

        ```
        - What's the weather forecast?
        - Ask if I'd like to turn off the lights
        ```
        > ⚠️ **Important:** Prompts must be **fewer than 255 characters**, or the call will fail.


### Good luck!
Now you can use your Alexa skill to integrate and interact with Home Assistant via voice using Assist or open your favorite dashboard on the Echo Show.
If you enjoyed it, remember to send a **Thank You** to the developers.

<details><summary>Special thanks</summary>
<p>   
To [rodrigoscoelho](https://github.com/rodrigoscoelho), who started the development of this skill.
</p>
</details>