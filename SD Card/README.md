# ✅✅✅ using SD Card ♦️ module [my video ⤵️](https://youtu.be/ySJt3QGyr9w)

<p float="left">
  <img src="../img/youtube_1.jpg" width="100%" />
</p>

# [my video ⤴️](https://youtu.be/ySJt3QGyr9w) Complete explain the project 

# guide [video_1](https://youtu.be/JxjZXf7veMM) , [video_2](https://youtu.be/naZYr5vY4Sg) , [video_3](https://youtu.be/mFsLlakhVL8)

To host a website using your ESP-WROOM-32 module and a microSD card, you can follow these general steps:

```
ESP-WROOM-32                    MicroSD Card Module
  |                                  |
  | CS (GPIO X) ---------------------| CS
  | SCK (GPIO Y) --------------------| SCK
  | MOSI (GPIO Z) -------------------| MOSI
  | MISO (GPIO W) -------------------| MISO
  | 3.3V ----------------------------| VCC (or 3.3V)
  | GND -----------------------------| GND
```



### CODE

```cpp
#include <WiFi.h>
#include <SPI.h>
#include <SD.h>
#include <ESPAsyncWebServer.h>

// Pin Definitions
#define SD_CS    5   // Replace with the GPIO pin number connected to CS of microSD card module
#define SD_SCK   18  // Replace with the GPIO pin number connected to SCK of microSD card module
#define SD_MOSI  23  // Replace with the GPIO pin number connected to MOSI of microSD card module
#define SD_MISO  19  // Replace with the GPIO pin number connected to MISO of microSD card module

const char* ssid = "spa";
const char* password = "12345678";

AsyncWebServer server(80);

void setup() {
  // Initialize Serial and SD card
  Serial.begin(115200);
  
  // Print Wi-Fi connection details
  Serial.print("Connecting to ");
  Serial.println(ssid);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  int tries = 0;
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
    tries++;
    if (tries > 20) {
      Serial.println("Failed to connect to WiFi");
      break;
    }
  }

  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("");
    Serial.println("WiFi connected");
    Serial.print("IP address: ");
    Serial.println(WiFi.localIP());
  } else {
    Serial.println("WiFi connection failed");
    return;
  }

  // Initialize SD card
  if (!SD.begin(SD_CS)) {
    Serial.println("Card Mount Failed");
    return;
  }
  
  // Check if index.html exists
  if (!SD.exists("/index.html")) {
    Serial.println("index.html missing");
    return;
  }

  // Route for serving files from SD card
  server.onNotFound([](AsyncWebServerRequest *request){
    String path = request->url();

    // Check if SD card is still connected
    if (!SD.begin(SD_CS)) {
      request->send(503, "text/html", "<html><body><h1>SD card not connected</h1></body></html>");
      return;
    }

    // Serve index.html by default if path ends with '/'
    if(path.endsWith("/")) path += "index.html";

    String contentType = "text/plain";
    if(path.endsWith(".html")) contentType = "text/html";
    else if(path.endsWith(".css")) contentType = "text/css";
    else if(path.endsWith(".js")) contentType = "application/javascript";

    File file = SD.open(path.c_str());
    if(file){
      request->send(SD, path.c_str(), contentType);
      file.close();
    } else {
      request->send(404, "text/html", "<html><body><h1>File not found</h1></body></html>");
    }
  });

  // Start server
  server.begin();
}

void loop() {
  // Nothing to do here
}

```

<p float="left">
  <img src="SD web (1).jpg" width="45%" />
  <img src="SD web (2).jpg" width="45%" />
</p>

<p float="left">
  <img src="../img/sd.jpg" width="100%" />
</p>
