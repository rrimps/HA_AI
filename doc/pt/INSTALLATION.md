
## INSTALAÇÃO

> ⚠️ **Before continuing**, garante que tudo está fncionando com o Assist no Home Assistant e sua instância do Home Assistant está exposta para Internet com um domínio e um certificado HTTPS válido.

## Table of Contents

1. [Criando a Skill Alexa](#criando-a-skill-alexa)
2. [Obtendo o home_assistant_agent_id](#obtendo-o-home_assistant_agent_id-do-assist-ou-da-ia-generativa-se-estiver-utilizando-uma)
3. [Obtendo o home_assistant_token](#obtendo-o-home_assistant_token-token-de-longa-duração)
4. [Configurando o Invocation Name](#configurando-o-invocation-name)
5. [Publicando a Skill](#publicando-a-skill)
6. [Ativando o reconhecimento de cômodo](#ativando-o-reconhecimento-de-cômodo-só-funciona-com-ia)
7. [Ativando iniciador de conversa com prompt do Home Assistant](#ativando-iniciador-de-conversa-com-prompt-do-home-assistant)


## Criando a Skill Alexa

1. Crie uma Skill na [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask) seguindo os passos abaixo:
   - **Name your Skill**: Escolha um nome de sua preferência (Ex: Home Assistant Assist)
   - **Choose a primary locale**: Portuguese (BR)
   - **Choose a type of experience**: Other
   - **Choose a model**: Custom
   - **Hosting services**: Alexa hosted (Python)
   - **Hosting region**: US East (N. Virginia) é o padrão, mas é necessário utilizar a mesma região onde criou a conta na AWS e configurou o IAM [Instruções aqui](https://www.home-assistant.io/integrations/alexa.smart_home)
   - **Templates**: Clique em ``Import skill``
   - **Insert the address**: [https://github.com/fabianosan/HomeAssistantAssist.git](https://github.com/fabianosan/HomeAssistantAssist.git) e clique ``Import``
2. Vá na aba **Code**
3. Insira suas informações no arquivo de configuração conforme instruções abaixo:
   - Abra o arquivo `config.cfg` no diretório raiz do projeto (/Skill Code/lambda/).
   - Insira as seguintes informações (configurações obrigatórias):
     ```txt
     home_assistant_url=https://SUA-URL-EXTERNA-DO-HOME-ASSISTANT
     home_assistant_token=SEU-TOKEN-DO-HOME-ASSISTANT
     ```
   - **home_assistant_url**: URL externa do seu Home Assistant (caminho raiz).
   - **home_assistant_token**: Token de acesso de longa duração do seu Home Assistant.   

   - Você pode adicionar essas configurações `opcionais` também (mas atente-se às instruções que cada uma exige):
     ```txt
     home_assistant_agent_id=SEU-AGENT-ID
     home_assistant_language=pt-BR
     home_assistant_room_recognition=False
     home_assistant_dashboard=ID-SEU-DASHBOARD
     home_assistant_kioskmode=False
     ask_for_further_commands=False
     assist_input_entity=input_text.assistant_input
     debug=true
     ```
   - **(opcional) home_assistant_agent_id**: ID do agente de conversação configurado no seu Home Assistant, se não configurado, será utilizado o Assist (Padrão).
   - **(opcional) home_assistant_language**: Idioma para chamar a API de conversação do Home Assistant. Se não configurado, será utilizado o padrão do agente definido.
   - **(opcional) home_assistant_room_recognition**: Ative o modo de identificação de área do dispositivo com `True`. **Atenção**, só funciona com IA e precisa de configurações adicionais no Home Assistant. _Se utiliza o Assist padrão, desative essa opção, pois nenhum comando irá funcionar com ela ativada e sem a configuração adequada._
   - **(opcional) home_assistant_dashboard**: Caminho do dashboard para exibir na echoshow, ex.: `mushroom`, se não configurado, irá carregar o "lovelace"
   - **(opcional) home_assistant_kioskmode**: Ative o modo quisque com `True`. **Atenção**, só ative essa opção se tiver o [componente kiosk mode](https://github.com/maykar/kiosk-mode) instalado.
   - **(opcional) ask_for_further_commands**, Ative novas perguntas com `True`. Esta variável determina se a Alexa perguntará por mais comandos após responder. Defina como `True` para ativar este comportamento ou `False` para desativá-lo. O padrão é `False`.
   - **(opcional) assist_input_entity**: Ativando funcionalidade de iniciar uma conversa com prompt do Home Assistant `input_text.assistant_input`. **Atenção**, essa funcionalidade requer [configurações extras no Home Assistant](#ativando-iniciador-de-conversa-com-prompt-do-home-assistant).
   - **(opcional) debug**, Ativa o debug com `True`. Defina esta variável para registrar as mensagens de depuração.
   
4. Se desejar, altere as respostas da skill no arquivo `/locale/pt-BR.lang` ou outro idioma suportado.
5. Após qualquer alteração feita, seja no código ou configuração, clique em `Save`.
6. Depois clique em `Deploy`.
7. Por fim, volte na aba `Build` e clique em `Build skill`.

## Obtendo o `home_assistant_agent_id` do Assist ou da IA generativa (se estiver utilizando uma)

- Com seu Home Assistant aberto, navegue até a **Ferramentas de Desenvolvedor**, vá na aba `Ações` e siga os passos abaixo: 
1. Busque por `conversation.process` no campo de ações e selecione:

  ![Ação: Conversação: Processo](images/dev_action.png)

2. Ative o campo `Agente` e selecione o agente de conversação desejado na lista:

  ![Ação: Agente](images/dev_action_uimode.png)

3. Alterne para o `MODO YAML` e copie o ID que está no campo `agent_id`:

  ![Ação: Agente ID](images/dev_action_yaml.png)

## Obtendo o `home_assistant_token` (Token de longa duração)

- Com seu Home Assistant aberto, navegue até o seu perfil de usuário, no canto inferior esquerdo, clique e depois vá na aba `Segurança` na parte superior: 
  1. No final da página, clique no botão `CRIAR TOKEN`:
  2. Digite o nome que achar adeuqado, ex.: `Skill Home Assistant Assist` e clique em em `OK`:

    ![Criar token](images/token.png)

  3. Copie o token:

    ![Token criado](images/token_created.png)

  4. Coloque o token gerado no arquivo de configuração.

## Configurando o ``Invocation Name``

- O nome de invocação padrão configurado no código é "casa inteligente".
- Para alterar o nome de invocação:
  1. Vá para a aba **Build**.
  2. Clique em `Invocations` e depois em `Skill Invocation Name`.
  3. Insira o novo nome de invocação desejado e salve as alterações (teste se essa palavra de ativação pode ser usada na aba de **Test**).
  4. Dê rebuild do modelo clicando `Build skill` se alterar.
  
## Publicando a Skill

1. Após fazer o deploy do código na aba **Code**, volte para aba **Build** e clique em **Build skill**.
2. Depois vá no aplicativo **Alexa** em seu celular e vá em: `Mais` > `Skills e jogos` > deslize a tela até o fim e clique em `Suas Skills` > `Desenv.`, clique na skill que você acabou de criar e **ative**

    ![Desenv. skills](images/alexa_dev_app.jpg)
    ![Ativar para uso](images/alexa_dev_app_activated.jpg)

3. Volte no console da ``Alexa Developer Console`` e teste a Skill na aba **Test** para garantir que a palavra de ativação e a skill estão funcionando corretamente.

## Ativando o reconhecimento de cômodo (só funciona com IA)
- Nesse modo, a skill envia o device id (do dispositivo `echo` que está executando a skill) na chamada da API de conversação do Home Assistant, então com uma instrução de comando para a IA e um rótulo associado no dispositivo, a IA consegue identificador os dispositivo da mesma área onde está localizado sua `Alexa`, para ativar, siga os passos abaixo:

  ***Atenção !***
  ## Esse modo deixa os comandos mais lentos e e exige configurações mais complexas:
  1. Altere a configuração `home_assistant_room_recognition` para `True` e faça um novo `deploy` e um novo `Build Model` da skill;
  2. Ative o log de debug da API de conversação adicionando a seguinte configuração no `configuration.yaml` do Home Assistant:
  - Insira a seguinte informação:
     ```txt
     logger:
       logs:
         homeassistant.components.conversation: debug
     ```
  3. Reinicie o Home Assistant e inicie a skill pelo dispositivo echo desejado, depois de ativado, o log mostrará a instrução recebida pela skill conforme o exemplo abaixo:
    ```txt
    2024-10-10 11:04:56.798 DEBUG (MainThread) [homeassistant.components.conversation.agent_manager] Processing in pt-BR: ligue a luz da sala. device_id: amzn1.ask.device.AMAXXXXXX
     ```
     Você também pode obter o device_id no log "device: " pela ``Alexa Developer Console`` em ``Cloud Watch`` se souber como fazê-lo.
  4. Pegue todo o identificador que estiver após o device_id, ex.: `amzn1.ask.device.AMAXXXXXX` e adicione um novo rótulo no **dispositivo echo** pela Integração `Alexa Media Player`:
  
    ![Rótulo no dispositivo echo com o device ID recebido da skill](images/echo_device_label.png)
    
  5. Atualize o **prompt de comando da IA** de sua preferência com a instrução abaixo:
     ```txt
     Se solicitado uma ação em um dispositivo e sua área não for fornecida, capture o identificador contido após o "device_id:" no comando, obtenha o rótulo com mesmo identificador e associe a área desse rótulo ao dispositivo para saber área o dispositivo pertence.
     ```

## Ativando iniciador de conversa com prompt do Home Assistant

#### Esta configuração adiciona o recurso de prompter para permitir conversas da Alexa iniciadas a partir do Home Assistant

> ⚠️ **Antes de continuar**, garanta que você possui o [Alexa Media Player](https://github.com/alandtse/alexa_media_player) instalado e configurado.

1. Ative a configuração na skill da Alexa:
   - Adicione a seguinte linha ao seu arquivo `config.cfg`:

     ```
     assist_input_entity = input_text.assistant_input
     ```

2. Crie um Auxiliar de Texto no Home Assistant:

    1. Abra o Home Assistant.
    2. Vá em: **Configurações → Dispositivos e Serviços → Auxiliares**
    3. Clique em **Criar Auxiliar** → Escolha **Texto**.
    4. Defina as seguintes opções:
        - **Nome:** `assistant_input`
        - **Número máximo de caracteres:** `255` (este é o limite rígido)
    5. Clique em **Criar**.

    > ⚠️ Nota: 255 caracteres é uma limitação rígida para o tamanho do prompt. Ainda não há uma solução confiável, exceto incorporar outras entradas de texto no prompt.

3. Crie um Script no Home Assistant:

    1. Vá até o [Console do Desenvolvedor Alexa](https://developer.amazon.com/alexa/console/ask).
    2. Na página inicial da sua skill, clique em **Copiar ID da Skill**.
    3. No Home Assistant, vá para **Configurações → Automatizações e Cenas → Scripts**.
    4. Clique em **Adicionar Script** e dê um nome como `Prompt Alexa Device`.
    5. Clique no menu de três pontos (⋮) e mude para o **modo YAML**.
    6. Cole o seguinte YAML no editor.  
       Substitua os espaços reservados:
         - `*your Skill ID*` → pelo ID real da sua skill Alexa  
         - `*the alexa you want to target*` → pelo ID da entidade `media_player` do seu dispositivo Alexa

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
               O prompt a ser enviado para a skill, usado como a primeira mensagem para iniciar uma conversa.
             required: true
         ```

    7. Clique em **Salvar**.

4. Chame o Script a partir de uma Automação

    Agora que o script está configurado, você pode acioná-lo a partir de uma automação. Isso irá:
      - Enviar um prompt para sua skill da Alexa;
      - Iniciar uma conversa falada com a resposta do assistente.
      ### Exemplo de Ação de Automação

      ```
      action: script.prompt_alexa_device
      metadata: {}
      data:
        prompt: >-
          Sugira que eu lembre de trancar todas as portas e janelas da casa antes de sair
      ```

      Outros exemplos de prompt:

      ```
      - Qual e a previsão do tempo?
      - Pergunte se eu gostaria de desligar as luzes
      ```

      > ⚠️ **Importante:** Os prompts devem ter **menos de 255 caracteres**, ou a chamada não funcionará.



### Boa sorte!
Agora você pode usar sua skill Alexa para integrar e interagir com o Home Assistant via Assist por voz ou abrir a tela do seu dashboard preferido na Echoshow.
Se gostou, lembre-se de mandar um **Obrigado** para os desenvolvedores.

<details><summary>Agradecimentos</summary>
<p>   
Ao [rodrigoscoelho](https://github.com/rodrigoscoelho), quem iniciou o desenvolvimento desta skill.
</p>
</details>