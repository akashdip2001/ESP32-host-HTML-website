# ESP32-host-HTML-website

# ✅ HTML in CPP

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

# big HTML => CPP & using as array[] [video](https://youtu.be/CPCTf20Sbvw)

<p float="left">
  <img src="img/1 (1).png" width="49%" />
  <img src="img/1 (2).png" width="49%" />
</p>

# ✅ HTML => PowerShell with .NET Classes

```kotlin
/YourSketchFolder
├── YourSketchName.ino
├── data
│   ├── index.html
│   ├── style.css
│   └── script.js
```

move => Arduino IDE => File => Preferences => SketchBook Location => where 📁 "sketch_jun28a.ino" <br />
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

# alternate process (use .h file without directly convert using cmd) [video](https://youtu.be/eHxkZ7poKHc)

advantage of this process - it's suitable for using javascript 

# ❌ HTML in ESP32's SPIFFS (SPI Flash File System)

goTo [GitHub:](https://github.com/me-no-dev/arduino-esp32fs-plugin) => [Releases](https://github.com/me-no-dev/arduino-esp32fs-plugin/releases/tag/1.1) => download ESP32FS-1.1.zip <br />
IDE => Files => Preferences => ScatchBook Location => create filder "tools" => copy-pest the folder 📂"ESP32FS" => Restart the IDE

# guide [video](https://youtu.be/0ox1oF9pSus)

If you're encountering a "404 Not Found" error when trying to access `http://192.168.150.165/`, it typically indicates that the server (your ESP32 in this case) is running and reachable, but it couldn't find the requested resource (`index.html` or any other file you specified). Here are a few steps to troubleshoot and resolve this issue:

### Steps to Troubleshoot:

1. **Check SPIFFS Upload:**
   - Ensure that you have successfully uploaded your `index.html` file and any other necessary files to SPIFFS. Use the SPIFFS upload tool in Arduino IDE or PlatformIO to verify that the files are indeed transferred to the ESP32.

2. **Verify File Path:**
   - Double-check the path and filename used in your `server.on("/", ...)` handler. Ensure that it matches exactly with the file name (`index.html`) and path in SPIFFS where you uploaded it.

3. **Serial Monitor Output:**
   - Review the serial monitor output for any errors or messages indicating issues with SPIFFS initialization or file serving.

4. **HTTP Request Handling:**
   - Confirm that the server is correctly handling HTTP requests. In your case, the handler should respond to requests to the root path ("/") with the `index.html` file.

### Adjusted Code Example:

```cpp
#include <WiFi.h>
#include <AsyncTCP.h>
#include <ESPAsyncWebServer.h>
#include <SPIFFS.h>

// Replace with your network credentials
const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

AsyncWebServer server(80);

void setup() {
    Serial.begin(115200);
    delay(1000); // Allow time for serial monitor to open

    // Connect to Wi-Fi
    Serial.println();
    Serial.print("Connecting to Wi-Fi...");
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }
    Serial.println("Connected to WiFi");

    // Print ESP32 local IP address
    Serial.print("IP Address: ");
    Serial.println(WiFi.localIP());

    // Initialize SPIFFS
    if (!SPIFFS.begin(true)) {
        Serial.println("An error occurred while mounting SPIFFS");
        return;
    }
    Serial.println("SPIFFS initialized");

    // Route for root / web page
    server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
        request->send(SPIFFS, "/index.html", "text/html");
    });

    // Start server
    server.begin();
    Serial.println("HTTP server started");
}

void loop() {
    // Keep the server running
    // Not much to do here, but keep the server alive
}
```

### Additional Checks:

- **Clear Cache:** Sometimes browsers cache 404 errors. Clear your browser cache or try accessing the IP address from a different browser or incognito/private window.
  
- **Check IP Address:** Ensure that the IP address printed in the serial monitor (`Serial.println(WiFi.localIP())`) matches the one you're trying to access in the browser.

- **Network Configuration:** If the problem persists, check your network configuration and ensure there are no firewall rules or network settings preventing access to the ESP32.

By following these steps and ensuring everything is configured correctly, you should be able to resolve the "404 Not Found" error and successfully access your ESP32 hosted web page at `http://192.168.150.165/`. <br />
output

```
Connecting to Wi-Fi........Connected to WiFi
IP Address: 192.168.150.165
SPIFFS initialized
HTTP server started
```
