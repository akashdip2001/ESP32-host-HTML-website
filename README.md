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

# HTML => PowerShell with .NET Classes

move => Arduino IDE => File => Preferences => SketchBook Location => where "sketch_jun28a.ino" 
& convart the index.html => index_html_gz.h using PowerShell

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

# HTML in ESP32's SPIFFS (SPI Flash File System)
