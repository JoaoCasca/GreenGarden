# GreenGarden – Sistema de Rega Automática (Arduino + ESP32-C6)

**Autor(es):** GreenGarden Team (João Casca, José Quaresma)  
**Versão:** 0.1.0  
**Data da versão:** 2025-11-11

---

## Descrição
GreenGarden é um protótipo de **rega automática** com **monitorização local** (LCD 16x2) e **controlo remoto** via **hotspot ESP32-C6** (servidor web).  
O Arduino lê os **sensores de humidade do solo** e **luz (LDR)** e aciona uma **bomba de água** através de **transístor TIP120** e **díodo flyback 1N4007**. O **ESP32-C6** cria um ponto de acesso (AP) e uma página web para **visualizar o estado** e **alternar** entre **AUTO/MANUAL**.

---

## Pré‑requisitos
- **Arduino IDE** 2.x (ou PlatformIO)
- **Placa Arduino** (ex.: UNO/Nano) com **porta USB** funcional
- **ESP32 core** instalado no Arduino IDE (incluindo **ESP32-C6**)
  - Boards Manager → “esp32” (Espressif) → instalar
  - Placa: “**ESP32C6 Dev Module**” (ou equivalente)
- **Bibliotecas Arduino**:
  - `LiquidCrystal` (incluída por defeito, para LCD 16x2)
- **Hardware mínimo**:
  - Sensor humidade do solo (saída analógica)
  - LDR + resistor para divisor de tensão
  - Bomba 5V (ou 6–12V com fonte dedicada)
  - **TIP120** (Darlington) + **1N4007** (flyback) + **resistor base**
  - LCD 16x2 (modo 4 bits) + potenciómetro contraste
  - Botão (override MANUAL) + **LED RGB** e **buzzer** (opcional)
  - Cabos/jumpers, breadboard, fonte adequada

> **Segurança elétrica:** usar **díodo flyback** na bobina da bomba e **massa comum** entre Arduino e o circuito de potência.

---

## Estrutura do Repositório
```
/firmware/
  /arduino/            # Leitura sensores, LCD, lógica de rega + UART
  /esp32/              # Hotspot + servidor web (HTML embutido) + UART
/hardware/
  wiring.pdf           # Diagrama de ligações (quando disponível)
  bom.xlsx             # Lista de materiais (quando disponível)
/docs/
  README.md            # Este ficheiro
  LICENSE              # Licença do projeto
```

---

## Instalação & Compilação
### Arduino (sensores/atuadores)
1. Abrir **Arduino IDE** → `firmware/arduino/`  
2. Selecionar placa (ex.: **Arduino Uno**) e **porta**.  
3. Ajustar pinos no código conforme o teu circuito (LCD, sensor, TIP120, botão).  
4. Compilar e **carregar**.

### ESP32-C6 (hotspot + web)
1. Abrir **Arduino IDE** → `firmware/esp32/`  
2. Selecionar **ESP32C6 Dev Module**.  
3. Configurar **SSID/PASSWORD** do AP no ficheiro `config.h` (ou nas `#define`).  
4. Compilar e **carregar**.

---

## “Getting Started” – Execução & Modos
1. **Ligar o Arduino**: o LCD deverá mostrar **humidade/luz** e **estado**.  
2. **Ligar o ESP32-C6**: procurar a rede **`GreenGarden`** e ligar com a **password** definida.  
3. Abrir o **browser** em `http://192.168.4.1/` (IP AP por defeito) para ver métricas e **toggle AUTO/MANUAL**.  
4. **Modo AUTO**: a bomba liga quando a **humidade < limiar** e desliga com **histerese**.  
5. **Modo MANUAL**: usar **botão físico** (override) ou **toggle web** para ativar/desativar a bomba.

---

## Parâmetros (configuráveis)
| Parâmetro                | Onde (ex.)             | Valor padrão | Descrição                                                       |
|-------------------------|------------------------|--------------|-----------------------------------------------------------------|
| `HUMIDITY_THRESHOLD_ON` | firmware/arduino       | `35` (%)     | Abaixo deste valor, **liga** a bomba (modo AUTO).               |
| `HUMIDITY_THRESHOLD_OFF`| firmware/arduino       | `45` (%)     | Acima deste valor, **desliga** (histerese).                     |
| `SAMPLE_INTERVAL_MS`    | firmware/arduino       | `1000`       | Intervalo de amostragem para sensores/LCD.                      |
| `DEBOUNCE_MS`           | firmware/arduino       | `50`         | Debounce do botão físico.                                       |
| `UART_BAUD`             | ambos                  | `115200`     | Baudrate da ligação Arduino ↔ ESP32.                            |
| `AP_SSID`               | firmware/esp32/config  | `GreenGarden`| SSID do hotspot ESP32-C6.                                       |
| `AP_PASSWORD`           | firmware/esp32/config  | `greengarden`| Password do hotspot (mín. 8 chars).                             |
| `AUTO_MODE_DEFAULT`     | firmware/arduino       | `true`       | Estado inicial (AUTO vs MANUAL).                                |

> Recomenda-se guardar parâmetros de utilizador em `config.h` (ignorado pelo git) ou em **EEPROM/NVS** para persistência.

---

## Notas
- O **LCD 16x2** mostra (linha 1) humidade/luz; (linha 2) **modo** e **bomba ON/OFF**.  
- O **LED RGB** e **buzzer** são opcionais e fornecem **feedback local** (ex.: vermelho=bomba ON).  
- A versão inicial não usa cloud; o controlo é **local** via AP do ESP32-C6.  
- Para apresentação/relatório, registar **humidade antes/depois** da rega e **3 ciclos** sem oscilações.

---

## Como contribuir
1. **Fork** do repositório e cria uma branch feature: `git checkout -b feature/nome`  
2. **Commits concisos** com mensagens claras (Português ou Inglês).  
3. **Pull Request** com descrição do objetivo e testes (quando aplicável).  
4. Segue a priorização **MoSCoW** no Trello/GitHub Projects e atualiza a documentação se alterares parâmetros.

---

## Licença
Este projeto é disponibilizado sob a **MIT License** (ver ficheiro `LICENSE`).  
Se a tua escola exigir outra licença, substitui o ficheiro e atualiza esta secção.

---

## Agradecimentos
Baseado em práticas dos LABs da UC (LAB3–LAB7) e na arquitetura Arduino + ESP32-C6.
