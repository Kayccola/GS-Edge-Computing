integrantes:
Kayque Amaro-RM572031
Giulia Russo-RM57
Kauan Merida-RM573997
Victoria Bandeira-RM57
Enzo Oldani-RM571685

---

# # ORBIT MIND - Monitoramento Preventivo de Fadiga Cognitiva

##  Link e Demonstração do Projeto

https://wokwi.com/projects/465967595195384833
<img width="689" height="721" alt="image" src="https://github.com/user-attachments/assets/049ac3ad-595d-4693-8992-e7ffde42498b" />

---

## Descrição do Projeto

O **ORBIT MIND** é um protótipo focado em saúde mental e segurança operacional para profissionais que atuam em ambientes de alta pressão, como missões aeroespaciais e centros de controle de missões críticas. O dispositivo funciona como um sistema inteligente capaz de processar dados em tempo real para monitorar os níveis de atenção, estresse e exaustão psicológica do usuário, visando mitigar o risco de acidentes causados por fadiga.

## Objetivo da Solução

Evitar falhas humanas provocadas pelo esgotamento mental e prevenir o desenvolvimento do Burnout. A solução coleta os indicadores lógicos do usuário e gera alertas preventivos, avisando tanto o operador quanto a central de controle no exato momento em que os limites saudáveis de estresse são ultrapassados. Isso possibilita uma tomada de decisão rápida, como a pausa programada ou a substituição do profissional antes que um erro crítico aconteça.

---

## Componentes Utilizados

Para simular essa tecnologia no ambiente do **Wokwi**, estruturamos o circuito utilizando os seguintes elementos eletrônicos:

* **Placa Microcontroladora:** Arduino Uno (responsável por processar a lógica do sistema).
* **Sensor Simulador:** Potenciômetro Deslizante (atua simulando a variação dos dados biométricos e cognitivos do usuário).
* **Interface Visual:** Display OLED SSD1306 via I2C (exibe o relatório de dados em tempo real).
* **Alerta Sonoro:** Buzzer Piezoelétrico (emite o sinal de alarme intermitente em caso de risco emergencial).

---

## Explicação do Funcionamento

O sistema opera através de um ciclo contínuo de varredura e resposta imediata:

1. **Captura de Dados:** O Arduino faz a leitura analógica do potenciômetro. Movimentar o controle para a direita simula o avanço do cansaço físico e mental do operador.
2. **Processamento:** O código converte a leitura analógica em uma escala percentual simples (de 0% a 100% de fadiga).
3. **Monitoramento Visual:** A métrica é enviada para a tela OLED, atualizando constantemente o painel com a mensagem `"Fadiga: X%"`.
4. **Ação Preventiva (Tomada de Decisão):**
* **Abaixo de 75%:** O sistema entende que as condições são seguras. A tela exibe `Status: Operação Segura` e os alarmes permanecem desligados.
* **A partir de 75%:** O limite de segurança é quebrado, indicando alto risco de exaustão. A tela muda para `ALERTA: RISCO BURNOUT` e o buzzer começa a apitar de forma intermitente, exigindo atenção imediata.



---

## Estrutura do Circuito (Mapeamento de Pinos)

| Componente | Pino no Componente | Pino de Conexão no Arduino | Tipo de Sinal |
| --- | --- | --- | --- |
| **Display OLED** | GND / VCC | GND / 5V | Alimentação |
| **Display OLED** | SDA | A4 | Comunicação I2C |
| **Display OLED** | SCL | A5 | Comunicação I2C |
| **Potenciômetro** | GND / VCC | GND / 5V | Alimentação |
| **Potenciômetro** | SIG (Meio) | A0 | Entrada Analógica |
| **Buzzer** | GND / SIG | GND / Pino 11 | Saída Digital (PWM) |

---

## Instruções de Execução

1. Acesse o link do simulador fornecido no topo deste documento.
2. Certifique-se de que as bibliotecas `Adafruit SSD1306` e `Adafruit GFX Library` estejam instaladas no gerenciador de bibliotecas lateral.
3. Clique no botão **Play (Start Simulation)**.
4. Com a simulação rodando, utilize o mouse para arrastar o controle deslizante do potenciômetro:
* Mantenha o controle no início para observar o status estável de operação segura.
* Desloque o controle além de 3/4 do caminho para testar o acionamento do alarme sonoro e visual de risco de burnout.

##  Explicação Breve do Código
O firmware foi desenvolvido em C++ para a plataforma Arduino e se divide em três pilares principais:

Configuração Inicial (setup): Inicializa a comunicação serial para monitoramento e ativa o barramento I2C para comunicação com a tela OLED. Caso a tela não seja detectada no endereço gráfico (0x3C), o sistema trava por segurança. Uma tela de abertura com o nome "ORBIT MIND" é exibida por 2 segundos.

Mapeamento de Dados: No loop principal (loop), a função analogRead(A0) captura o valor gerado pelo potenciômetro (que varia de 0 a 1023). Logo em seguida, a função map() converte essa leitura matemática em uma porcentagem de 0 a 100%, tornando o dado interpretável como nível de fadiga.

Condicional de Segurança: O código avalia o percentual obtido. Se o valor for menor que 75%, ele limpa os alarmes com o comando noTone(). Se atingir 75% ou mais, a função tone(buzzer, 1000) é acionada em conjunto com o comando delay() para criar um efeito sonoro intermitente de alerta, enquanto a tela OLED atualiza os textos dinamicamente via funções de desenho gráfico (display.display()).

## Código Completo
```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

const int pinoPotenciometro = A0;
const int buzzer = 11;

void setup() {
  Serial.begin(9600);
  pinMode(buzzer, OUTPUT);

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { 
    Serial.println(F("Erro: Display OLED nao encontrado!"));
    for(;;);
  }
  
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(30, 20);
  display.println("ORBIT MIND");
  display.setCursor(20, 40);
  display.println("Inicializando...");
  display.display();
  delay(2000);
}

void loop() {
  int leitura = analogRead(pinoPotenciometro);
  int nivelEstresse = map(leitura, 0, 1023, 0, 100);

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  
  display.setCursor(15, 0);
  display.println("--- ORBIT MIND ---");
  
  display.setCursor(0, 22);
  display.setTextSize(2);
  display.print("Fadiga: ");
  display.print(nivelEstresse);
  display.println("%");

  display.setTextSize(1);
  display.setCursor(0, 52);

  if (nivelEstresse < 75) {
    display.println("Status: Operacao Segura"); 
    noTone(buzzer);
  } else {
    display.println("ALERTA: RISCO BURNOUT"); 
    tone(buzzer, 1000); 
    delay(150);
    noTone(buzzer);
  }

  display.display();
  delay(200);
}
```
