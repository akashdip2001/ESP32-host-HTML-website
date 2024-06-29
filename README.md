# ESP32-host-HTML-website

# HTML in CPP

```cpp
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "YourHotspotSSID";
const char* password = "YourHotspotPassword";

WebServer server(80);

void setup() {
    Serial.begin(115200);

    // Connect to Wi-Fi
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }
    Serial.println("Connected to WiFi");

    // Print ESP32 local IP address
    Serial.println(WiFi.localIP());

    // Route for root / web page
    server.on("/", HTTP_GET, [](){
        server.send(200, "text/plain", "Hello from ESP32!");
    });

    // Start server
    server.begin();
    Serial.println("HTTP server started");
}

void loop() {
    server.handleClient();
}

```

<p float="left">
  <img src="img/1 (1).png" width="49%" />
  <img src="img/1 (2).png" width="49%" />
</p>

# HTML => PowerShell with .NET Classes

```kotlin
/YourSketchFolder
├── YourSketchName.ino
├── data
│   ├── index.html
│   ├── style.css
│   └── script.js
```

move => Arduino IDE => File => Preferences => SketchBook Location => where "sketch_jun28a.ino" <br />
& convart the index.html => index_html_gz.h using PowerShell

<p float="left">
  <img src="img/2 (3).png" width="49%" />
  <img src="img/2 (4).png" width="49%" />
</p>

```powershell
$inputFile = "C:\Users\akash\OneDrive\Documents\Arduino\data\index.html.gz"
$outputFile = "C:\Users\akash\OneDrive\Documents\Arduino\sketch_jun28a\index_html_gz.h"

$fileBytes = [System.IO.File]::ReadAllBytes($inputFile)

$hexString = "const unsigned char index_html_gz[] PROGMEM = {" + [System.Environment]::NewLine
$hexArray = @()

foreach ($byte in $fileBytes) {
    $hexArray += "0x" + $byte.ToString("X2")
}

$hexString += ($hexArray -join ", ") + [System.Environment]::NewLine
$hexString += "};" + [System.Environment]::NewLine
$hexString += "const int index_html_gz_len = sizeof(index_html_gz) / sizeof(index_html_gz[0]);" + [System.Environment]::NewLine

$hexString | Out-File -FilePath $outputFile -Encoding ASCII
```
<p float="left">
  <img src="img/2 (1).png" width="49%" />
  <img src="img/2 (2).png" width="49%" />
</p>

```cpp
#include <WiFi.h>
#include <WebServer.h>
#include "index_html_gz.h" // Include the generated header file

const char* ssid = "spa";
const char* password = "12345678";

WebServer server(80);

void setup() {
    Serial.begin(115200);

    // Connect to Wi-Fi
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }
    Serial.println("Connected to WiFi");

    // Print ESP32 local IP address
    Serial.println(WiFi.localIP());

    // Route for root / web page
    server.on("/", HTTP_GET, [](){
        server.sendHeader("Content-Encoding", "gzip");
        server.send_P(200, "text/html", reinterpret_cast<const char*>(index_html_gz), index_html_gz_len);
    });

    // Start server
    server.begin();
    Serial.println("HTTP server started");
}

void loop() {
    server.handleClient();
}
```

in Bionary [index_html_gz.h (Generated header file):]

<p float="left">
  <img src="img/2 (7).png" width="100%" />
</p>
<p float="left">
  <img src="img/2 (5).png" width="49%" />
  <img src="img/2 (6).png" width="49%" />
</p>

# HTML in ESP32's SPIFFS (SPI Flash File System)

goTo [GitHub:](https://github.com/me-no-dev/arduino-esp32fs-plugin) => [Releases](https://github.com/me-no-dev/arduino-esp32fs-plugin/releases/tag/1.1) => download ESP32FS-1.1.zip <br />
IDE => Files => Preferences => ScatchBook Location => create filder "tools" => copy-pest the folder "ESP32FS" => Restart the IDE

