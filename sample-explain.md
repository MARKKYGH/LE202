### SAMPLE EXPLAIN
## EX1
#include <Arduino.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
}

void loop()
{
	cnt++;
	Serial.printf("PATTANI :%d\n",cnt);
	int s = cnt % 5 + 1;
	int d = s * 1000;
	delay(d);
}
### ตัวโปรแกรมจะเริ่มประกาศ cnt เริ่มต้น 1 ครั้ง
### วนลูปเพิ่มทีละ 1 ไปเรื่อยๆ
### มีดีเลย์ 300 ms
## EX2
#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	WiFi.mode(WIFI_STA);
	WiFi.disconnect();
	delay(100);
	Serial.println("\n\n\n");
}

void loop()
{
	Serial.println("========== เริ่มต้นแสกนหา Wifi ===========");
	int n = WiFi.scanNetworks();
	if(n == 0) {
		Serial.println("NO NETWORK FOUND");
	} else {
		for(int i=0; i<n; i++) {
			Serial.print(i + 1);
			Serial.print(": ");
			Serial.print(WiFi.SSID(i));
			Serial.print(" (");
			Serial.print(WiFi.RSSI(i));
			Serial.println(")");
			Serial.print(WiFi.channel(i));
			delay(10);
		}
	}
	Serial.println("\n\n");
	delay(10 * 1000);
}
### ตัวโปรแกรมเริ่มด้วยการประกาศตัวเลข cnt
### ทำการset wifi ให้พร้อม โดยมีดีเลย์ 100 ms
### วนลูปแสดงข้อความค้นหา wifi
### หาไม่เจอ แสดงข้อความ( NO NETWORK FOUND )
### ถ้าเจอก็แสดงให้เราเลือกใช้
## EX3
#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, OUTPUT);
	Serial.println("\n\n\n");
}

void loop()
{
	cnt++;
	if(cnt % 2) {
		Serial.println("========== ON ===========");
		digitalWrite(0, HIGH);
	} else {
		Serial.println("========== OFF ===========");
		digitalWrite(0, LOW);
	}
	delay(500);
}
### ตัวโปรแกรมจะเริ่มประกาศตัวเลข cnt
### set up กำหนดหน้าที่ของสัญญาณ (pinMode) ให้ port 0 เป็น output
### cnt เป็นเลขคู่ จะแสดงข้อความ ON และเปิด
### cnt เป็นคี่ แสดงข้อความ off และปิด
### ดีเลย์ 500 ms
## EX4
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, INPUT);
	pinMode(2, OUTPUT);
	Serial.printIn("\n\n\n");
}

void loop()
{
	int val = digitalRead(0);
	Serial.printf("======= read %d\n", val);
	if(val==1) {
		digitalWrite(2, LOW);
	} else {
	    digitalWrite(2, LOW);
	}
	delay(100);
}
### set port 0 is input and port 2 is output
### ลูปให้อ่านข้อมูลจาก port 0 แสดงข้อความอ่านได้เท่าไหร่
### 1 ให้ LOW ไปที่ port 2 ไฟก็จะดับ
### 0 ให้ HIGH ไปที่ port 2 ไฟก็จะติด
## EX5
#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "HI_BMFWIFI_2.4G";
const char* password = "0819110933";

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.mode(WIFI_STA);
	WiFi.begin(ssid, password);
	while (WiFi.status() != WL_CONNECTED) {
		delay(500);
		Serial.print(".");
	}
	Serial.print("\n\nIP address: ");
	Serial.println(WiFi.localIP());

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	/// http://192.0.0.1/ = Hello cnt: ???
	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});
	/// http://192.0.0.1/on = Hello cnt: ???
	server.on("/on", []() {
		cnt++;
		String msg = "Switch on ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});
	/// http://192.0.0.1/off = Switch off: ???
	server.on("/off", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
### ใส่ชื่อ ssid and password
### เลือก Server
### เริ่มรับสัญญาณ ดีเลย์ 500 ms
### โปรแกรมจะแสดง password ออกมา
### เชื่อมต่อไม่ได้ จะขึ้นว่า Path Not Found
### ถ้าได้จะแสดง Hello cnt: (โดยที่ cnt +1 )
### HTTP Server started
## EX6
#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "MY-ESP8266";
const char* password = "choompol";

IPAddress local_ip(192, 168, 1, 1);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 255, 0);

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.softAP(ssid, password);
	WiFi.softAPConfig(local_ip, gateway, subnet);
	delay(100);

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
### กำหนด ssid and password
### ตั้ง local ip , geteway , subnet
### เริ่มต้นการทำงานของโปรแกรม
### เชื่อมต่อได้จะขึ้น Hello cnt: (โดยที่ cnt +1)
