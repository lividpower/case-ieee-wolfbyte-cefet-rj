//Declaração das variáveis globais do programa
#include <LiquidCrystal_I2C.h> 
LiquidCrystal_I2C lcd(39, 16, 2); //declaração do lcd(16x2) conectaddo ao endereço 39
const int max_pontos = 50; //número máximo de rodadas 
int notas[4] = {262, 294, 330, 349}; // Notas musicais que serão utilizadas para o acionamento dos botões
int botoes[4] = {4, 3, 2, 1}; //pino dos botões
int leds[4] = {9, 8, 7, 6}; //pino dos leds
int nivel = 0;
int sequencia[max_pontos] = {}; //usado para armazenar a sequência de acendimento dos leds
bool gameOver = false; //usada para verificar a ocorrência do Game Over 
bool gameWin = false; //usada para conseguir diferenciar o Game Over de uma vitória do jogador
int recorde_atual = 0; //utilizada para verificação do recorde dentro do decorrer do programa
int recorde_fixo = 0; //registra o recorde do jogador (zera sempre que é cortada a alimentação do arduino)
int endereco = 0; //endereço onde será armazenado o valor do recorde na memória do microcontrolador
int rodada = 0; //usado para identificar a primeira rodada, quando for printado o início do jogo
int* ponteiro_pressionado;
int inicio = 0;
int jogada = 0;
 

bool verificacao() //verifica se o jogador inseriu corretamente a sequência de leds acesos
{
    int resultado;
    resultado = *ponteiro_pressionado; //recebe o botão que foi pressionado pelo jogador
    if (sequencia[jogada] != resultado) //compara jogada a jogada se o jogador errou a sequência de leds
    {
        //nesse caso, o jogador terá errado a sequência
        for (int i = 0; i <= 3; i++) 
        {   
            digitalWrite(leds[i], HIGH);
            tone(10, 70);
            delay(100);
            digitalWrite(leds[i], LOW);
            noTone(10);
        }
        return gameOver = true; //o jogador errou, portanto, game over!
    }
    return false;
}


void nivel_seguinte() //caso o jogador não cometa nenhum erro, a função irá passar para o nível seguinte
{
    int aleatorio = random(4); //sorteio do led que será aceso
    sequencia[nivel] = aleatorio; //esse led sorteado será atribuído para cada nivel que for sendo alcançado, formando uma sequência
    nivel++;
  	recorde_atual++;
}


void btn_pressionado() //verifica qual botão que foi pressionado pelo jogador 
{
    int pressionado;  //variável que indica a posição do botão pressionado dendo array de botões
    for (int i = 0; i < nivel; i++) 
    {
        bool aux = false;
        while (!aux) //looping infinito, processando o aperto de um botão
        {
            for (int i = 0; i <= 3; i++) 
            {
                if (digitalRead(botoes[i]) == HIGH) //verificando se o botão foi pressionado
                {
                    pressionado = i;
                    digitalWrite(leds[i], HIGH);  //quando for apertado o botão, o led referente ao botão será aceso
                    delay(18); //delay para evitar que uma nota musical se sobressaia sobre a outra
                    tone(10, notas[i]);
                    delay(300); 
                    digitalWrite(leds[i], LOW);
                    noTone(10); 
                    aux = true; //condição de saída desse looping
                }
            }
        }
        ponteiro_pressionado = &pressionado;
        if (verificacao())
            break;
        jogada++; 
    } 
 	jogada = 0; //a cada nível, essa variável zera
}


void piscar_led() //acendimento dos leds que forem sorteados
{
    for (int i = 0; i < nivel; i++) 
    {
        digitalWrite(leds[sequencia[i]], HIGH); //acendimento do led
        tone(10, notas[sequencia[i]]); //irá emitir um som com a nota musical referente ao led que foi acesso
        delay(500); //delay para que seja notório o acendimento do led, atrasando o seu desligamento
        digitalWrite(leds[sequencia[i]], LOW);
        noTone(10);  //encerra a emissão do som
        delay(100); //atrasa o acendimento do próximo led
    }
}


void setup() // Declaração dos pinos do ARDUINO
{
  	lcd.init(); //incialização do lcd
    lcd.backlight(); //acendendo a luz de fundo do lcd
    pinMode(10, OUTPUT); // Buzzer
    pinMode(6, OUTPUT); // Led amarelo
    pinMode(7, OUTPUT); // Led verde
    pinMode(8, OUTPUT); // Led azul
    pinMode(9, OUTPUT); // Led vermelho
    pinMode(4, INPUT); // Botão vermelho
    pinMode(3, INPUT); // Botão azul
    pinMode(2, INPUT); // Botão verde
    pinMode(1, INPUT); // Botão amarelo
    randomSeed(analogRead(A0)); // Geração de uma semente randômica para evitar que o jogo reinicia de forma padronizada
}
 

void loop() // execução em looping
{ 
  lcd.clear();
  if(nivel == 0 && rodada == 0)
  {
	lcd.setCursor(0,0);
    lcd.print("INICIANDO JOGO");
    lcd.setCursor(0,1);
    lcd.print("EM:");
    delay(2000); //delay para o inicío do próximo jogo
    lcd.clear();
    lcd.print("5 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("4 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("3 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("2 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("1 SEGUNDO");
    delay(1000);
    lcd.clear();
  }
  lcd.print("NIVEL:");
  lcd.setCursor(6,0); 
  lcd.print(nivel + 1);
  if(recorde_fixo <= recorde_atual) //tentando solucionar o problema da primeira execução, onde existiria um possível "lixo" dentro desse endereço
  {
    recorde_fixo = recorde_atual;
    lcd.setCursor(0,1);
    lcd.print("NOVO RECORDE:"); //caso o recorde seja quebrado...
    lcd.setCursor(14, 1);
    lcd.print(recorde_fixo);
    delay(500);
    lcd.setCursor(0,1);
    lcd.print("               "); //limpando apenas uma linha do LCD
  }
  lcd.setCursor(0,1);
  lcd.print("RECORDE:");
  lcd.setCursor(9,1);
  lcd.print(recorde_fixo);
  nivel_seguinte(); //reinício automático do jogo
  piscar_led();
  btn_pressionado();
  if(recorde_fixo < recorde_atual)
  {
  	recorde_fixo = recorde_atual; //inserindo o novo valor do recorde na memória
  }
  if(nivel == max_pontos) //se o jogador chegar no último nível, ele terá zerado o jogo
  {
    for(int i = 0; i<=3; i++)
    {
      digitalWrite(leds[i],HIGH);
      delay(100);
      digitalWrite(leds[i],LOW);
    }
    gameOver = true;
    gameWin = true; //verificação para caso o jogador tenha zerado o jogo
  }
  if (gameOver == true) 
  { //caso existisse um menu, ele teria que ser inserido a partir daqui...
   	lcd.setCursor(0,0);
    if(!gameWin) //se o jogador tiver zerado o jogo, irá pular essa parte...
    {   lcd.clear();
    	lcd.print("GAME OVER!");
    	delay(3000);
    	lcd.clear(); 
    }
    else
    {   lcd.clear();
    	lcd.print("VICTORY!");
      	delay(3000);
      	lcd.clear();
    }
    sequencia[max_pontos] = {}; //zerando as variáveis para que o jogador possa reiniciar o jogo
    nivel = 0; 
    gameOver = false;
    inicio = 0;
    recorde_atual = 0;
    jogada = 0;
    rodada = -1;
    lcd.print("REINICIANDO JOGO");
    lcd.setCursor(0,1);
    lcd.print("EM:");
    delay(2000); //delay para o inicío do próximo jogo
    lcd.clear();
    lcd.print("5 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("4 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("3 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("2 SEGUNDOS");
    delay(1000); 
    lcd.clear();
    lcd.print("1 SEGUNDO");
  }
  //inserir o salvamento do recorde na memória
  delay(1000);
}

//colocar os recordes enquanto o jogador for jogando 
//fixar valor do recorde mesmo após o desligamento do ARDUINO
//tentar pensar em um MENU para inciar e reiniciar o jogo, ao invés de deixar o jogo reiniciar automaticamente
//tentar pensar em um MENU para inciar e reiniciar o jogo, ao invés de deixar o jogo reiniciar automaticamente
