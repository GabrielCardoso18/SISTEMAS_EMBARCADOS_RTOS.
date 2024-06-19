# MQTT (18/06/2024)
<p align="justify">
O MQTT (Message Queuing Telemetry Transport) é um protocolo de mensagens leve ideal para conectar dispositivos IoT de maneira eficiente em redes com largura de banda limitada.\
Além de ser simples de implementar e usar, o MQTT oferece diferentes níveis de entrega de mensagens, garantindo confiabilidade aos dispositivos IoT conectados.\
Amplamente utilizado, o MQTT conecta e controla dispositivos inteligentes, coleta dados de sensores distribuídos para monitoramento e integra dispositivos em automação residencial ou industrial, facilitando a comunicação entre dispositivos IoT de maneira escalável através do uso de tópicos para direcionar mensagens aos destinatários adequados. </p>

## MQTT -Na Prática
* Publish/Publicação: Dispositivos enviam mensagens para um tópico específico.
* Subscribe/Assinatura: Dispositivos interessados se inscrevem nos tópicos para receber as mensagens.
* Broker MQTT: Serve como intermediário que recebe mensagens publicadas e as encaminha aos dispositivos assinantes dos tópicos correspondentes.

## Código Comentado

    #include <Arduino.h>
    #include <ESP8266WiFi.h>
    #include <PubSubClient.h>
    
    const char* ssid = "GABRIEL";  // Nome da rede WiFi
    const char* password = "12345678";  // Senha da rede WiFi
    const char* mqtt_server = "192.168.10.85";  // Endereço IP do servidor MQTT
    
    WiFiClient espClient;  // Cliente WiFi
    PubSubClient client(espClient);  // Cliente MQTT usando o cliente WiFi
    const int Relay = D1;  // Pino ao qual está conectado o relé
    
    void setup_wifi() {
      delay(10);
      Serial.println();
      Serial.print("Connecting to ");
      Serial.println(ssid);
      WiFi.begin(ssid, password);  // Conecta à rede WiFi especificada
      while (WiFi.status() != WL_CONNECTED) {  // Aguarda até que a conexão seja estabelecida
        delay(500);
        Serial.print(".");
      }
      Serial.println("");
      Serial.println("WiFi connected");
      Serial.println("IP address: ");
      Serial.println(WiFi.localIP());  // Imprime o endereço IP atribuído ao ESP8266
    }
    
    void callback(char* topic, byte* payload, unsigned int length) {
      Serial.print("Message arrived [");
      Serial.print(topic);
      Serial.print("] ");
      String message;
      for (int i = 0; i < length; i++) { 
        Serial.print((char)payload[i]);  // Imprime cada byte da mensagem recebida
        message += (char)payload[i];  // Constrói a string da mensagem recebida
      }
      Serial.println(message);  // Imprime a mensagem completa no Serial Monitor
    
      if (String(topic) == "ControleRelay") {  // Verifica se o tópico da mensagem é "ControleRelay"
        if (message == "ON") {
          digitalWrite(Relay, LOW);  // Liga o relé se a mensagem for "ON"
        } else if (message == "OFF") {
          digitalWrite(Relay, HIGH);  // Desliga o relé se a mensagem for "OFF"
        }
      }
    }
    
    void setup() {
      pinMode(Relay, OUTPUT);  // Configura o pino do relé como saída
      digitalWrite(Relay, HIGH);  // Inicialmente desliga o relé
      Serial.begin(9600);  // Inicializa o Serial Monitor com baud rate 9600
      setup_wifi();  // Inicia a conexão WiFi
      client.setServer(mqtt_server, 1883);  // Configura o servidor MQTT e porta
      client.setCallback(callback);  // Define a função de callback para mensagens MQTT
    }
    
    void reconnect() {
      while (!client.connected()) {  // Enquanto não estiver conectado ao servidor MQTT
        Serial.print("Attempting MQTT connection...");
        if (client.connect("ESP8266Client")) {  // Tenta se reconectar ao servidor MQTT
          Serial.println("connected");
          client.subscribe("ControleRelay");  // Se conectado, inscreve-se no tópico "ControleRelay"
        } else {
          Serial.print("failed, rc=");
          Serial.print(client.state());  // Exibe o estado da conexão MQTT
          Serial.println(" try again in 5 seconds");
          delay(5000);  // Espera 5 segundos antes de tentar novamente
        }
      }
    }
    
    void loop() {
      if (!client.connected()) {
        reconnect();  // Se não estiver conectado, tenta reconectar
      }
      client.loop();  // Mantém a comunicação com o servidor MQTT
    }

## Prints do Broker, Subscribe e Publish
![Imagem do WhatsApp de 2024-06-18 à(s) 21 41 47_134f2456](https://github.com/GabrielCardoso18/SISTEMAS_EMBARCADOS_RTOS./assets/126261772/7c7fe41c-61a6-4667-b9d7-f13368054b99)
![Imagem do WhatsApp de 2024-06-18 à(s) 21 41 48_6ce50f8f](https://github.com/GabrielCardoso18/SISTEMAS_EMBARCADOS_RTOS./assets/126261772/25fb97ae-f0cd-423e-a901-a057671c4cef)

# SISTEMAS_EMBARCADOS_RTOS.
### Esquemático
![image](https://github.com/GabrielCardoso18/SISTEMAS_EMBARCADOS_RTOS./assets/126261772/8f718c03-2c86-4645-bcdc-3cf9335dd994)
### PCB
![image](https://github.com/GabrielCardoso18/SISTEMAS_EMBARCADOS_RTOS./assets/126261772/2de2ba51-4c8f-45c3-b272-6135e1c4ea63)
### 3D
![image](https://github.com/GabrielCardoso18/SISTEMAS_EMBARCADOS_RTOS./assets/126261772/5f40afdb-8d64-4677-8e4c-39dfbab772be)
![image](https://github.com/GabrielCardoso18/SISTEMAS_EMBARCADOS_RTOS./assets/126261772/56114c2c-07c9-4596-a161-a5f9115989b2)




