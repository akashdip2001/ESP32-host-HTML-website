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

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif">

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

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif">

# ❌ HTML in ESP32's SPIFFS (SPI Flash File System)

goTo [GitHub:](https://github.com/me-no-dev/arduino-esp32fs-plugin) => [Releases](https://github.com/me-no-dev/arduino-esp32fs-plugin/releases/tag/1.1) => download ESP32FS-1.1.zip <br />
IDE => Files => Preferences => ScatchBook Location => create filder "tools" => copy-pest the folder 📂"ESP32FS" => Restart the IDE

# guide [video_1](https://youtu.be/0ox1oF9pSus) , [video_2](https://youtu.be/LcMVoKb1NhE)

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

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif">

# ✅ using SD Card ♦️ module 

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
  <img src="img/SD web (1).jpg" width="45%" />
  <img src="img/SD web (2).jpg" width="45%" />
</p>


