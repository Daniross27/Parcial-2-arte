## 2\. Tabla Consolidada de Mapeo de Pines (ESP32 de 30 Pines)

| Componente                           | Pin del Componente                                                   | Pin en ESP32 (30 Pines)                                                                                                            | Notas de Montaje                                                                                                |
| ------------------------------------ | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Ultrasónico (HC-SR04)**            | VCC GND TRIG ECHO                                                    | **VIN / 3.3V** **GND** **GPIO 18** **GPIO 19**                                                                                       | TRIG es salida digital.ECHO es entrada digital (usar divisor de voltaje a 3.3V si es posible).                  |
| **Fotoresistencia (LDR)**            | Terminal 1 Unión LDR + Resistencia 330 | **3.3V** **GPIO 32** **GND**                                                                                                       | La señal de entrada al pin GPIO 32 sale del punto medio entre la LDR y la resistencia de $330\\Omega$. |
| **Temperatura (LM35)**               | VCC (Pin 1) Vout (Pin 2) GND (Pin 3)                                 | **VIN / 5V** **GPIO 35** **GND**                                                                                                   | Entrada analógica ADC1 (GPIO 35). Entrega $10\\text{ mV/}^\\circ\\text{C}$.                                     |
| **Temp. / Humedad (DHT11)**          | VCC GND DATA / OUT                                                   | **3.3V** **GND** **GPIO 4**                                                                                                        | E/S Digital (Bus 1-Wire).                                                                                       |
| **Lector RFID (RC522)**              | VCC GND RST MISO MOSI SCK SDA (SS)                                   | **3.3V** *(¡NUNCA 5V!)* **GND** **GPIO 22** **GPIO 19** **GPIO 23** **GPIO 18** **GPIO 5**                                         | Bus SPI Hardware estándar del ESP32.                                                                            |
| **Pantalla OLED (SH1106 / SSD1306)** | VCC GND SDA SCL                                                      | **3.3V / 5V** **GND** **GPIO 21** **GPIO 22**                                                                                      | Bus I2C Hardware (SDA = GPIO 21, SCL = GPIO 22). Dirección I2C habitual: `0x3C`.                                |
| **LED RGB (Cátodo Común)**           | Cátodo (Pin largo) Rojo (R) Verde (G) Azul (B)                       | **GND** **GPIO 25** (+ Resistencia $330\\Omega$) **GPIO 26** (+ Resistencia $330\\Omega$) **GPIO 27** (+ Resistencia $330\\Omega$) | Salidas PWM/Digitales para formar colores de 0 a 255 con `analogWrite()`.                                       |


### 1\. Fotoresistencia (LDR) — Divisor de Tensión con 10 kΩ

```
      ESP32 (30 Pines)                     PROTOBOARD / LDR
    +------------------+                 +--------------------+
    |             3.3V |----------------&gt;|  Terminal 1 LDR    |
    |                  |                 |     (  LDR  )      |
    |          GPIO 32 |&lt;================|  Terminal 2 LDR    |
    |                  |        +-------&gt;|        +           |
    |                  |        |        |  Resistencia 330kΩ  |
    |              GND |--------+-------&gt;|  Terminal 2 Resis. |
    +------------------+                 +--------------------+

  Esquema eléctrico equivalente:
  [ 3.3V ESP32 ] ---&gt; [ LDR ] ---&gt; ( Punto Medio -&gt; GPIO 32 ) ---&gt; [ Resistencia 330Ω ] ---&gt; [ GND ESP32 ]

```

---

### 2\. LED RGB Cátodo Común — Con 3 Resistencias de 330 Ω

```
      ESP32 (30 Pines)                     LED RGB (Cátodo Común)
    +------------------+                 +-------------------------+
    |          GPIO 25 |----[ R 330Ω ]--&gt;| Pin Rojo (R)            |
    |          GPIO 26 |----[ R 330Ω ]--&gt;| Pin Verde (G)           |
    |          GPIO 27 |----[ R 330Ω ]--&gt;| Pin Azul (B)            |
    |              GND |----------------&gt;| Cátodo Común (Pata (-) larga) |
    +------------------+                 +-------------------------+

```

---

### 3\. Sensor Ultrasónico (HC-SR04) — Divisor de Voltaje en ECHO

```
      ESP32 (30 Pines)                     SENSOR HC-SR04
    +------------------+                 +------------------+
    |         VIN (5V) |----------------&gt;| VCC              |
    |              GND |----------------&gt;| GND              |
    |          GPIO 18 |----------------&gt;| TRIG             |
    |          GPIO 19 |&lt;------------&gt;| ECHO (Salida 5V) |
    +------------------+                 +------------------+

```

---

### 4\. Sensor de Temperatura Analógico (LM35)

```
      ESP32 (30 Pines)                     SENSOR LM35 (Vista frontal)
    +------------------+                 +------------------+
    |         VIN (5V) / 3.3 |----------------&gt;| Pin 1: VCC (3.3V) |
    |          GPIO 35 |&lt;----------------| Pin 2: Vout (10mV/°C)
    |              GND |----------------&gt;| Pin 3: GND       |
    +------------------+                 +------------------+

  Vista física del encapsulado del LM35 (Mirando la cara PLANA con letras):
         +-----------+
         |   LM35    |
         |           |
         +-----------+
           |   |   |
           1   2   3
           |   |   |
          VCC Vout GND
         (+5V/3.3V)(GPIO35)

```

---

### 5\. Sensor de Temperatura y Humedad (DHT11)

```
  Caso A: Módulo PCB de 3 Pines (El más común en el laboratorio)
      ESP32 (30 Pines)                     MÓDULO DHT11 PCB
    +------------------+                 +------------------+
    |             3.3V |----------------&gt;| VCC / (+)        |
    |              GND |----------------&gt;| GND / (-)        |
    |           GPIO 4 |&lt;===============&gt;| DATA / OUT / S   |
    +------------------+                 +------------------+


```

---

### 6\. Lector RFID (RC522) — Comunicación SPI

```
      ESP32 (30 Pines)                     MÓDULO RFID RC522
    +------------------+                 +------------------+
    |             3.3V |----------------&gt;| 3.3V (¡NUNCA 5V!)|
    |              GND |----------------&gt;| GND              |
    |          GPIO 22 |----------------&gt;| RST              |
    |          GPIO 19 |&lt;----------------| MISO             |
    |          GPIO 23 |----------------&gt;| MOSI             |
    |          GPIO 18 |----------------&gt;| SCK              |
    |           GPIO 5 |----------------&gt;| SDA / SS         |
    +------------------+                 +------------------+
```
| Sensor | Magnitud | Widget / Bloque Recomendado | ¿Por qué se utiliza este bloque? |
| :--- | :--- | :--- | :--- |
| Ultrasónico (HC-SR04) | Distancia en cm | Gauge (Medidor circular) o Line Chart | En el Laboratorio 5 se especifica usar un **Gauge**. Es un velocímetro/reloj circular ideal para ver distancias instantáneas configurando sus límites (por ejemplo, `0` a `100 cm`). |
| Fotorresistencia (LDR) | Nivel de Luz (0% a 100%) | Gauge o Stream / Value | Al ser un porcentaje relativo de iluminación, un **Gauge** graduado de `0` a `100%` muestra visualmente si el ambiente está oscuro o iluminado. |
| Temp. y Humedad (DHT11 / LM35) | °C y % de Humedad | Line Chart (Gráfica de líneas) o Gauge | El **Line Chart** permite graficar el comportamiento histórico y las variaciones de temperatura o humedad a lo largo del tiempo. |
| Sensor Digital / Presencia (PIR / Botón) | Estado lógico (`1` / `0`) | Indicator (Indicador numérico/color) | Muestra una luz tipo "faro" que se enciende en verde o rojo dependiendo de si detecta presencia o no. |
