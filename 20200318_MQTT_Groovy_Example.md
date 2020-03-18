{% include_relative menu.md %}

# 20200318 MQTT Groovy Example

I am interested in MQTT since the beginning of 2020.

This is Groovy example code for a client connecting to a MQTT server, which subscribes for a topic tree, parses payloads and publishes information.

I have seen the original version of this code here :

https://cumulocity.com/guides/device-sdk/mqtt-examples/#hello-mqtt-java

      package de.wartbar.mqtt

      import org.eclipse.paho.client.mqttv3.*
      import java.util.concurrent.*

      class MqttClientWartbar {

          def static startClient() {

              final String serverUrl = "tcp://localhost"
              final String clientId = "my_mqtt_java_client"
              final String tenant = ""
              final String username = "user"
              final String password = "password"

              final MqttConnectOptions options = new MqttConnectOptions()
              options.setUserName(tenant + "/" + username)
              options.setPassword(password.toCharArray())

              final MqttClient client = new MqttClient(serverUrl, clientId, null)
              client.connect(options)

              client.publish("client/" + clientId + "/status", ("registered").getBytes(), 2, true)

              client.subscribe("client/#", new IMqttMessageListener() {
                  public void messageArrived(final String topic, final MqttMessage message) throws Exception {
                      final String payload = new String(message.getPayload())

                      System.out.println("Received topic " + topic)
                      System.out.println("Received payload " + payload)
                      if (payload.startsWith("reactOn")) {
                          Executors.newSingleThreadScheduledExecutor().execute(new Runnable() {
                              public void run() {
                                  try {
                                      System.out.println("I react on :" + payload)
                                      client.publish("client/" + clientId + "/reaction1",("reacted on " + payload).getBytes(), 2, false)
                                      client.publish("client/" + clientId + "/reaction2", new MqttMessage(("reacted on " + payload).getBytes()))
                                 } catch (MqttException e) {
                                      e.printStackTrace()
                                  } catch (InterruptedException e) {
                                      e.printStackTrace()
                                  }
                              }
                          })
                      }
                  }
              })

              while (true) {
                  System.out.println(".")
                  sleep(1000)
              }
          }
      }
