# buildings
varies
//Tryharder
//programs semplify your life
#include <Wire.h>
#include <DS3231.h>
#include <LiquidCrystal_I2C.h>
#include <FastIO.h>
#include <I2CIO.h>
#include <LCD.h>
#include <LiquidCrystal.h>
#include <LiquidCrystal_SR.h>
#include <LiquidCrystal_SR2W.h>
#include <LiquidCrystal_SR3W.h>

#define LED 7  //led output
#define I2C_LCD_ADDR 0x27  //indirizzo lcd
#define BACKLIGHT_PIN 3  //schermo lcd acceso
#define En_pin 2  //schermo lcd
#define Rw_pin 1  //schermo lcd
#define Rs_pin 0  //schermo lcd
#define D4_pin 4  //schermo lcd
#define D5_pin 5  //schermo lcd
#define D6_pin 6  //schermo lcd
#define D7_pin 7  //schermo lcd

LiquidCrystal_I2C lcd(I2C_LCD_ADDR,En_pin,Rw_pin,Rs_pin,D4_pin,D5_pin,D6_pin,D7_pin);  // indirizzo display lcd

//***PIN PULSANTI***//
int forward=12;  //pin tasto enter
int backward=11;  //pin tasto back
int up=9;  //pin tasto up
int down=8;  //pin tasto down

//***INPUT PULSANTI***//
int val_forward;  //variabile utilizzata per sapere l'input del pulsante 
int val_backward;  //variabile utilizzata per sapere l'input del pulsante 
int val_up;  //variabile utilizzata per sapere l'input del pulsante 
int val_down;  //variabile utilizzata per sapere l'input del pulsante

//***STATO PULSANTI***//
int forwardstate;  //variabile utilizzata per sapere lo stato del pulsante
int backwardstate;  //variabile utilizzata per sapere lo stato del pulsante
int upstate;  //variabile utilizzata per sapere lo stato del pulsante
int downstate;  //variabile utilizzata per sapere lo stato del pulsante

//***ENTRATA/CONFERMA E USCITA***//
int enter=0;  //variabile utilizzata per entrare e confermare

//***SCORRIMENTO PARAMETRI MENU E SOTTOMENU***//
int stats=0;  //variabile utilizzata per scorrere opzioni menu

//***VISUALIZZAZIONE SINGOLA A DISPLAY***//
int printed=0;  //variabile utilizzata per scrivere una sola volta un menù o un opzione

//***ARRAY CON PARAMETRI MODIFICABILI***//
float toptemp[64]={0,1,2,3,4,5,5.33,5.66,6,6.33,6.66,7,7.33,7.66,8,8.33,8.66,9,9.33,9.66,10,10.33,10.66,11,11.33,11.66,12,12.33,12.66,13,13.33,13.66,14,14.33,14.66,15,15.33,15.66,16,16.33,16.66,17,17.33,17.66,18,18.33,18.66,19,19.33,19.66,20,20.33,20.66,21,21.33,21.66,22,22.33,22.66,23,23.33,23.66,24,25};  //array con valori di temperatura massima camera di fermentazione in gradi celsius
unsigned long coldstartup[16]={40000,50000,60000,70000,80000,90000,100000,110000,120000,130000,140000,150000,180000,360000,720000,1440000};  //array con valori di tempo di accensione raffreddamento in millisecondi
unsigned long coldidle[16]={480000,540000,600000,660000,720000,780000,840000,900000,960000,1020000,1080000,1140000,1200000,1500000,1800000,2400000};  //array con valori di tempo di standby raffreddamento in millisecondi
float bottomtemp[64]={0,1,2,3,4,5,5.33,5.66,6,6.33,6.66,7,7.33,7.66,8,8.33,8.66,9,9.33,9.66,10,10.33,10.66,11,11.33,11.66,12,12.33,12.66,13,13.33,13.66,14,14.33,14.66,15,15.33,15.66,16,16.33,16.66,17,17.33,17.66,18,18.33,18.66,19,19.33,19.66,20,20.33,20.66,21,21.33,21.66,22,22.33,22.66,23,23.33,23.66,24,25};  //array con valori di temperatura minima camera di fermentazione in gradi celsius
unsigned long heatstartup[16]={40000,50000,60000,70000,80000,90000,100000,110000,120000,130000,140000,150000,180000,360000,720000,1440000};  //array con valori di tempo di accensione riscaldamento in millisecondi
unsigned long heatidle[16]={480000,540000,600000,660000,720000,780000,840000,900000,960000,1020000,1080000,1140000,1200000,1500000,1800000,2400000};  //array con valori di tempo di standby riscaldamento in millisecondi
unsigned long standbyidle[16]={480000,540000,600000,660000,720000,780000,840000,900000,960000,1020000,1080000,1140000,1200000,1500000,1800000,2400000};  //array con valori di tempo di standby in millisecondi
int controltemp[4]={20,30,40,50};  //array con valori di temperatura centralina in gradi celsius
unsigned long mainmenu[4]={30000,45000,60000,120000};  //array con valori di tempo da attendere prima di tornare alla home del display in millisecondi
unsigned long backlight[4]={5000,15000,60000,2400000};  //array con valori di tempo di accensione illuminazione display lcd in millisecondi

//***LUNGHEZZA PREDEFINITA ARRAY***//
int size1=64;  //lunghezza array necessaria per settare le variabili al valore corrente
int size2=16;  //lunghezza array necessaria per settare le variabili al valore corrente
int size3=4;  //lunghezza array necessaria per settare le variabili al valore corrente
int size4=2;  //lunghezza array necessaria per settare le variabili al valore corrente
int size5=15;  //lunghezza array necessaria per calcolare la temperatura media

//***VARIABILI PER IMPOSTARE PARAMETRI***//
float maxtemp=19;  //variabile utilizzata per impostare la temperatura massima della camera di fermentazione in gradi celsius
float mintemp=18;  //variabile utilizzata per impostare la temperatura minima della camera di fermentazione in gradi celsius
unsigned long cstartup=70000;  //variabile utilizzata per impostare il tempo di accensione del raffreddamento
unsigned long cidle=600000;  //variabile utilizzata per impostare il tempo di standby del raffreddamento
unsigned long hstartup=60000;  //variabile utilizzata per impostare il tempo di accensione del riscaldamento
unsigned long hidle=600000;  //variabile utilizzata per impostare il tempo di standby del riscaldamento
unsigned long sbidle=600000;  //variabile utilizzata per impostare il tempo di standby generico del raffreddamento e del riscaldamento
int maxcputemp=30;  //variabile utilizzata per impostare la temperatura massima della centralina in gradi celsius
unsigned long home=30000;  //variabile utilizzata per tornare alla home dopo un certo tempo
unsigned long blon=60000;  //variabile utilizzata per impostare il tempo di accensione illuminazione display lcd

//***ARRAY FORCE START***//
int forcecold[2]={0,1};  //array con valori di force start
int forceheat[2]={0,1};  //array con valori di force start
int forcechamberfan[2]={0,1};  //array con valori di force start
int forcecpufan[2]={0,1};  //array con valori di force start

//***VARIABILI FORCE START***//
int fcold=0;  //variabile utilizzata per impostare il force start raffreddamento
int fheat=0;  //variabile utilizzata per impostare il force start raffreddamento
int fff=0;  //variabile utilizzata per impostare il force start raffreddamento
int fcf=0;  //variabile utilizzata per impostare il force start raffreddamento

//***STATO DEL SISTEMA***//
int mode=5;  //variabile utilizzata per sapere lo stato del sistema

//***ESEGUI OGNI 1 SECONDO***//
unsigned long clock1=1000;  //intervallo di tempo tra rilevazioni di temperatura
unsigned long onesec=0;  //variabile utilizzata per memorizzare il momento dell'ultima rilevazione di temperatura

//***ESEGUI OGNI 15 SECONDI***//
unsigned long clock15=15000;  //intervallo di tempo tra misurazioni a display
unsigned long fifteensec=0;  //variabile utilizzata per memorizzare il momento dell'ultima misurazione visualizzata a display

//***TIMER***//
unsigned long target;  //variabile utilizzata come timer accensioni/standby
unsigned long light;  //variabile utilizzata come timer accensioni illuminazione display lcd
unsigned long fan;  //variabile utilizzata come timer accensioni ventola camera di fermentazione
unsigned long fanon;  //variabile utilizzata per impostare il tempo di accensione ventilazione
unsigned long menu=0;  //variabile utilizzata come timer per tornare al menu principale

//***ACCENSIONI***//
byte refrigeration=LOW;  //stato raffreddamento spento
byte heating=LOW;  //stato riscaldamento spento
int cpufan=0;  //ventilazione centralina pwm spenta
byte ventilation=LOW;  //stato ventilazione camera di fermentazione

//***CONTATORI***//
int cscount=0;  //contatore accensioni raffreddamento
int hscount=0;  //contatore accensioni riscaldamento
int cicount=0;  //contatore stanbdy raffreddamento
int hicount=0;  //contatore standby riscaldamento
int sicount=0;  //contatore standby
int fcoldcount=0;  //contatore force start raffreddamento
int fheatcount=0;  //contatore force start riscaldamento
int fffcount=0;  //contatore force start ventilazione camera fermentazione
int fcfcount=0;  //contatore force start ventilazione cpu

//***RILEVAZIONE SEGNALE ANALOGICO***//
int16_t Val;  //valore analogico sonda di temperatura

//***VALORI DI CALCOLO TEMPERATURA***//
float Vbit=0.00012500381;  //costante di precisione (~5/4096 con adc)
float Kr=1;  //costante
float Rb=980;  //valore resistenza in serie alla sonda temperatura
float Vcc=4.90;  //tensione alimentazione sonda temperatura
float A=10;  //costante sonda ptc
float B=264069;  //costante sonda ptc
float C=6005;  //costante sonda ptc
float D=29341;  //costante sonda ptc
float E=4122425;  //costante sonda ptc
float V;  //voltaggio calcolato
float Ra;  //resistenza calcolata

//***RILEVAZIONI TEMPERATURA MEDIA CAMERA FERMENTAZIONE***//
float aft[15]={0,0,0,0,0,0,0,0,0,0,0,0,0,0,0};  //array per le rilevazioni di temperatura fermentazione ogni secondo per 15 secondi
float fermtemp;  //valore di temperatura della camera di fermentazione in gradi celsius rilevato ogni secondo
float fermtemptotal;  //variabile utilizzata per la somma delle temperature misurate contenute nell'array aft[i] e necessario per calcolare la media
float fermtempaverage=18;  //variabile utilizzata per memorizzare la temperatura media della camera di fermentazione

//***RILEVAZIONI TEMPERATURA MEDIA CPU***//
float act[15]={0,0,0,0,0,0,0,0,0,0,0,0,0,0,0};  //array per le rilevazioni di temperatura centralina ogni secondo per 15 secondi
float cputemp;  //valore di temperatura della centralina in gradi celsius rilevato ogni secondo
float cputemptotal;  //variabile utilizzata per la somma delle temperature misurate contenute nell'array act[q] e necessario per calcolare la media
float cputempaverage=25;  //variabile utilizzata per memorizzare la temperatura media della centralina

void setup(){
  pinMode(12,INPUT);  //imposta il pin digitale come input
  pinMode(11,INPUT);  //imposta il pin digitale come input
  pinMode(10,INPUT);  //imposta il pin digitale come input
  pinMode(9,INPUT);  //imposta il pin digitale come input
  pinMode(8,OUTPUT);  //imposta il pin digitale come input
  pinMode(7,OUTPUT);  //imposta il pin digitale come input
  pinMode(6,OUTPUT);  //imposta il pin digitale come input
  pinMode(5,OUTPUT);  //imposta il pin digitale come input
  lcd.begin(20,4);  //inizializza il display
  lcd.setBacklightPin(BACKLIGHT_PIN,POSITIVE);  //accendi la luce dell'lcd
  lcd.setBacklight(HIGH);  //accendi la luce dell'lcd
  lcd.home();  //vai alla home dell'lcd
  lcd.setCursor(6,1);  //setta il cursore in posizione
  lcd.print("Lovibrau");  //scrivi "Lovibrau"
  lcd.setCursor(4,2);  //setta il cursore in posizione
  lcd.print("fermentation");  //scrivi "fermentation"
  delay(4000);  //attendi 3 secondi
  lcd.clear();  //pulisci lo schermo
  lcd.setBacklight(LOW);
}

void loop(){
  switch(val_up){
    case 500:
    if(
  }
  //***COMANDI PER SCORRERE MENU E PARAMETRI***//
   if((long)(millis()-menu)>=0){  //se il tempo trascorso è maggiore di menu
    enter=0;  //resetta la variabile
    scroll1=0;  //resetta la variabile
    scroll2=0;  //resetta la variabile
    mod=0;  //resetta la variabile
    mod1=0;  //resetta la variabile
    mod2=0;  //resetta la variabile
    printed=0;  //resetta la variabile
  }
  val_enter=digitalRead(enter);  //leggi lo stato del pulsante enter
  val_quit=digitalRead(quit);  //leggi lo stato del pulsante quit
  val_up=digitalRead(up);  //leggi lo stato del pulsante up
  val_down=digitalRead(down);  //leggi lo stato del pulsante down
  if(val_up!=upstate){  //se l input del pulsante è diverso dalla variabile di stato
    upstate=val_up;  //salva l input del pulsante nella variabile di stato
    if(upstate==HIGH){  //se lo stato del pulsante è high
      return 1;
    }
  }
  if(val_down!=downstate){  //se l input del pulsante è diverso dalla variabile di stato
    downstate=val_down;  //salva l input del pulsante nella variabile di stato
    if(downstate==HIGH){  //se lo stato del pulsante è high
      return 2;
    }
  }
  //***MENU IN VISUALIZZAZIONE***//
  if(enter==0&&scroll1==0&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 0 e printed è uguale a 0
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("Temperature:");  //scrivi
    lcd.setCursor(12,0);  //posiziona il cursore a
    lcd.print(fermtemp,2);  //scrivi la variabile fermtemp con due cifre dopo la virgola
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("Status:");  //scrivi
    if(mode==1){  //se lo stato del sistema è uguale a 1
      lcd.setCursor(7,1);  //posiziona il cursore a
      lcd.print("cold on");  //scrivi
    }
    else if(mode==2){  //se lo stato del sistema è uguale a 2
      lcd.setCursor(7,1);  //posiziona il cursore a
      lcd.print("heat on");  //scrivi
    }
    else if(mode==3){  //se lo stato del sistema è uguale a 3
      lcd.setCursor(7,1);  //posiziona il cursore a
      lcd.print("cooling");  //scrivi
    }
    else if(mode==4){  //se lo stato del sistema è uguale a 4
      lcd.setCursor(7,1);  //posiziona il cursore a
      lcd.print("warming");  //scrivi
    }
    else if(mode==5){  //se lo stato del sistema è uguale a 5
      lcd.setCursor(7,1);  //posiziona il cursore a
      lcd.print("off");  //scrivi
    }
    lcd.setCursor(7,1);  //posiziona il cursore a
    lcd.print(mode);  //scrivi lo stato del sistema
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Cold cycles:");  //scrivi
    lcd.setCursor(12,2);  //posiziona il cursore a
    lcd.print(cscount);  //scrivi il contatore delle accensioni ciclo raffreddamento
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print("Heat cycles:");  //scrivi
    lcd.setCursor(12,3);  //posiziona il cursore a
    lcd.print(hscount);  //scrivi il contatore delle accensioni ciclo riscaldamento
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==1&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 1 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Max temperature     ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(maxtemp,2);  //scrivi la variabile maxtemp con due cifre dopo la virgola
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==2&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 2 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Min temperature     ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(mintemp,2);  //scrivi la variabile mintemp con due cifre dopo la virgola
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==3&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 3 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Cold startup time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(cstartup);  //scrivi la variabile cstartup
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==4&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 4 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Cold idle time      ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(cidle);  //scrivi la variabile cidle
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==5&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 5 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Heat startup time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(hstartup);  //scrivi la variabile hstartup
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==6&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 6 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Heat idle time      ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(hidle);  //scrivi la variabile hidle
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==7&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 7 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Standby idle time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(sbidle);  //scrivi la variabile sbidle
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==8&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 8 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Control temperature ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(maxcputemp);  //scrivi la variabile maxcputemp con due cifre dopo la virgola
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==9&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 9 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Exit from menu time ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(home);  //scrivi la variabile sbidle
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==0&&scroll1==10&&printed==0){  //se enter è uguale a 0 scroll1 è uguale a 10 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to modify     ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Backlight on time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(blon);  //scrivi la variabile blon
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  //***COMANDI PER ENTRARE NELLA MODALITA' MODIFICA***//
  if(enter==0&&scroll1==1){  //se enter è uguale a 0 e scroll1 è uguale a 1
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        for(int i=0;i<(size1-1);i++){
          if(toptemp[i]==maxtemp){
            mod=i;
            break;
          }
        }
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=1;  //setta la variabile a 1
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==2){  //se enter è uguale a 0 e scroll1 è uguale a 2
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        for(int i=0;i<(size2-1);i++){
          if(bottomtemp[i]==mintemp){
            mod=i;
            break;
          }
        }
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=2;  //setta la variabile a 2
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==3){  //se enter è uguale a 0 e scroll1 è uguale a 3
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        for(int i=0;i<(size2-1);i++){
          if(coldstartup[i]==cstartup){
            mod1=i;
            break;
          }
        }
        mod2=0;  //resetta la variabile
        enter=3;  //setta la variabile a 3
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==4){  //se enter è uguale a 0 e scroll1 è uguale a 4
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        for(int i=0;i<(size2-1);i++){
          if(coldidle[i]==cidle){
            mod1=i;
            break;
          }
        }
        mod2=0;  //resetta la variabile
        enter=4;  //setta la variabile a 4
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==5){  //se enter è uguale a 0 e scroll1 è uguale a 5
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        for(int i=0;i<(size2-1);i++){
          if(heatstartup[i]==hstartup){
            mod1=i;
            break;
          }
        }
        mod2=0;  //resetta la variabile
        enter=5;  //setta la variabile a 5
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==6){  //se enter è uguale a 0 e scroll1 è uguale a 6
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        for(int i=0;i<(size2-1);i++){
          if(heatidle[i]==hidle){
            mod1=i;
            break;
          }
        }
        mod2=0;  //resetta la variabile
        enter=6;  //setta la variabile a 6
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==7){  //se enter è uguale a 0 e scroll1 è uguale a 7
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        for(int i=0;i<(size2-1);i++){
          if(standbyidle[i]==sbidle){
            mod1=i;
            break;
          }
        }
        mod2=0;  //resetta la variabile
        enter=7;  //setta la variabile a 7
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==8){  //se enter è uguale a 0 e scroll1 è uguale a 8
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        for(int i=0;i<(size3-1);i++){
          if(controltemp[i]==maxcputemp){
            mod2=i;
            break;
          }
        }
        enter=8;  //setta la variabile a 8
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==9){  //se enter è uguale a 0 e scroll1 è uguale a 9
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        for(int i=0;i<(size3-1);i++){
          if(mainmenu[i]==home){
            mod2=i;
            break;
          }
        }
        enter=9;  //setta la variabile a 9
        printed=0;  //resetta la variabile
      }
    }
  }
  if(enter==0&&scroll1==10){  //se enter è uguale a 0 e scroll1 è uguale a 10
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l'input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è HIGH
        light=millis()+blon;  //imposta il timer di accensione display
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        for(int i=0;i<(size3-1);i++){
          if(backlight[i]==blon){
            mod2=i;
            break;
          }
        }
        enter=10;  //setta la variabile a 10
        printed=0;  //resetta la variabile
      }
    }
  }
  //***VISUALIZZAZIONE MENU DI MODIFICA***//
  if(enter==1&&printed==0){  //se enter è uguale a 1 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod>63||mod<0){  //se la variabile mod è maggiore di 63 o minore di 0
      mod=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("MODIFY PARAMETERS   ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Max temperature     ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(toptemp[mod],2);  //scrivi l indice dell array toptemp corrispondente al valore mod con due cifre dopo la virgola
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==2&&printed==0){  //se enter è uguale a 2 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod>63||mod<0){  //se la variabile mod è maggiore di 63 o minore di 0
      mod=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("MODIFY PARAMETERS   ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Min temperature     ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(bottomtemp[mod],2);  //scrivi l indice dell array bottomtemp corrispondente al valore mod con due cifre dopo la virgola
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==3&&printed==0){  //se enter è uguale a 3 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod1>15||mod1<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod1=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("MODIFY PARAMETERS   ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Cold startup time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(coldstartup[mod1]);  //scrivi l indice dell array coldstartup corrispondente al valore mod1
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==4&&printed==0){  //se enter è uguale a 4 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod1>15||mod1<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod1=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Cold idle time      ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(coldidle[mod1]);  //scrivi l indice dell array coldidle corrispondente al valore mod1
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==5&&printed==0){  //se enter è uguale a 5 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod1>15||mod1<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod1=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Heat startup time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(heatstartup[mod1]);  //scrivi l indice dell array heatstartup corrispondente al valore mod1
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==6&&printed==0){  //se enter è uguale a 6 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod1>15||mod1<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod1=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Heat idle time      ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(heatidle[mod1]);  //scrivi l indice dell array heatidle corrispondente al valore mod1
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==7&&printed==0){  //se enter è uguale a 7 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod1>15||mod1<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod1=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Standby idle time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(standbyidle[mod1]);  //scrivi l indice dell array standbyidle corrispondente al valore mod1
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==8&&printed==0){  //se enter è uguale a 8 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod2>3||mod2<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod2=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Control temperature ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(controltemp[mod2]);  //scrivi l indice dell array controltemp corrispondente al valore mod2
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==9&&printed==0){  //se enter è uguale a 9 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod2>3||mod2<0){  //se la variabile mod1 è maggiore di 3 o minore di 0
      mod2=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("MODIFY  PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("enter to set        ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Exit from menu time ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(mainmenu[mod2]);  //scrivi l indice dell array mainmenu corrispondente al valore mod2
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  if(enter==10&&printed==0){  //se enter è uguale a 10 e printed è uguale a 0
    menu=millis()+home;  //imposta il timer di ritorno al menu principale
    if(mod2>3||mod2<0){  //se la variabile mod1 è maggiore di 4 o minore di 0
      mod2=0;  //resetta la variabile
    }
    lcd.clear();  //pulisci lo schermo
    lcd.setCursor(0,0);  //posiziona il cursore a
    lcd.print("INSTANT PARAMETERS  ");  //scrivi
    lcd.setCursor(0,1);  //posiziona il cursore a
    lcd.print("set to modify       ");  //scrivi
    lcd.setCursor(0,2);  //posiziona il cursore a
    lcd.print("Backlight on time   ");  //scrivi
    lcd.setCursor(0,3);  //posiziona il cursore a
    lcd.print(backlight[mod2]);  //scrivi l indice dell array backlight corrispondente al valore mod2
    printed=1;  //setta la variabile a 1 per visualizzare una sola volta
  }
  //***COMANDI PER MODIFICARE PARAMETRI***//
  else if(enter==1){  //se enter è uguale a 1
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        maxtemp=toptemp[mod];  //salva il valore corrispondente alla variabile mod contenuto nell array toptemp nella variabile maxtemp
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=1;  //setta la variabile a 1
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==2){  //se enter è uguale a 2
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        mintemp=bottomtemp[mod];//salva il valore corrispondente alla variabile mod contenuto nell array bottomtemp nella variabile mintemp
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=2;  //setta la variabile a 2
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==3){  //se enter è uguale a 3
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        cstartup=coldstartup[mod1];  //salva il valore corrispondente alla variabile mod1 contenuto nell array coldstartup nella variabile cstartup
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=3;  //setta la variabile a 3
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==4){  //se enter è uguale a 4
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        cidle=coldidle[mod1];  //salva il valore corrispondente alla variabile mod1 contenuto nell array coldidle nella variabile cidle
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=4;  //setta la variabile a 4
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==5){  //se enter è uguale a 5
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        hstartup=heatstartup[mod1];  //salva il valore corrispondente alla variabile mod1 contenuto nell array heatstartup nella variabile hstartup
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=5;  //setta la variabile a 5
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==6){  //se enter è uguale a 6
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        hidle=heatidle[mod1];  //salva il valore corrispondente alla variabile mod1 contenuto nell array heatidle nella variabile hidle
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=6;  //setta la variabile a 6
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==7){  //se enter è uguale a 7
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        sbidle=standbyidle[mod1];  //salva il valore corrispondente alla variabile mod1 contenuto nell array standbyidle nella variabile sbidle
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=7;  //setta la variabile a 7
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==8){  //se enter è uguale a 8
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        maxcputemp=controltemp[mod2];  //salva il valore corrispondente alla variabile mod2 contenuto nell array controltemp nella variabile maxcputemp
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=8;  //setta la variabile a 8
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==9){  //se enter è uguale a 9
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        home=mainmenu[mod2];  //salva il valore corrispondente alla variabile mod2 contenuto nell array mainmenu nella variabile home
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=9;  //setta la variabile a 9
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  else if(enter==10){  //se enter è uguale a 10
    val_forward=digitalRead(forward);  //leggi lo stato del pulsante forward
    if(val_forward!=forwardstate){  //se l input del pulsante è diverso dallo stato del pulsante
      forwardstate=val_forward;  //salva il valore dell'input nella variabile di stato pulsante
      if(forwardstate==HIGH){  //se lo stato del pulsante è high
        blon=backlight[mod2];  //salva il valore corrispondente alla variabile mod2 contenuto nell array backlight nella variabile blon
        light=millis()+blon;  //imposta il timer di accensione display
        scroll1=10;  //setta la variabile a 10
        mod=0;  //resetta la variabile
        mod1=0;  //resetta la variabile
        mod2=0;  //resetta la variabile
        enter=0;  //resetta la variabile
        printed=0;  //resetta la variabile
      }
    }
  }
  //***OPERAZIONI DA COMPIERE OGNI TOT SECONDI***//
  if(millis()-onesec>=clock1){
    onesec=millis();  //esegui ogni 1 secondo
    V=0;  //resetta il valore
    Ra=0;  //resetta il valore
    Val=ads.readADC_SingleEnded(3);  //rileva il valore analogico
    V=Val*Vbit;  //calcola il voltaggio
    Ra=Rb*((Vcc/V)-Kr);  //calcola la resistenza
    fermtemp=((-A*(Ra*Ra))/B)+((C*Ra)/D)-(E/D);  //calcola la temperatura
    for(int i=0;i<(size5-1);i++){
      aft[i]=aft[i+1];  //ordina i valori spostandoli dall ultimo al primo bit
    }
    aft[size5-1]=fermtemp;  //assegna i valori rilevati all array dall ultimo bit
    cputemp=rtc.getTemp();  //ottieni la temperatura della centralina
    for(int q=0;q<(size5-1);q++){
      act[q]=act[q+1];  //ordina i valori spostandoli dall ultimo al primo bit
    }
    act[size5-1]=cputemp;  //assegna i valori rilevati all array dall ultimo bit
  }
  if(millis()-fifteensec>=clock15){
    fifteensec=millis();  //esegui ogni 15 secondi
    if(aft[0]!=0){  //condizione di avvio per evitare errori
      fermtemptotal=0;  //resetta la somma dei valori
      for(int i=0;i<size5;i++){
        fermtemptotal=fermtemptotal+aft[i];  //fai la somma dei valori rilevati
      }
      fermtempaverage=fermtemptotal/(float)size5;  //calcola la media dei valori rilevati
      if(enter==0&&printed==1){  //ogni 15 secondi e se enter è uguale a 0 aggiorna i dati a display
        lcd.clear();  //pulisci lo schermo
        lcd.setCursor(0,0);  //posiziona il cursore a
        lcd.print("Temperature:");  //scrivi
        lcd.setCursor(12,0);  //posiziona il cursore a
        lcd.print(fermtempaverage,2);  //scrivi la variabile fermtemp con due cifre dopo la virgola
        lcd.setCursor(0,1);  //posiziona il cursore a
        lcd.print("Status:");  //scrivi
        if(mode==1){  //se lo stato del sistema è uguale a 1
          lcd.setCursor(7,1);  //posiziona il cursore a
          lcd.print("cold on");  //scrivi
        }
        else if(mode==2){  //se lo stato del sistema è uguale a 2
          lcd.setCursor(7,1);  //posiziona il cursore a
          lcd.print("heat on");  //scrivi
        }
        else if(mode==3){  //se lo stato del sistema è uguale a 3
          lcd.setCursor(7,1);  //posiziona il cursore a
          lcd.print("cooling");  //scrivi
        }
        else if(mode==4){  //se lo stato del sistema è uguale a 4
          lcd.setCursor(7,1);  //posiziona il cursore a
          lcd.print("warming");  //scrivi
        }
        else if(mode==5){  //se lo stato del sistema è uguale a 5
          lcd.setCursor(7,1);  //posiziona il cursore a
          lcd.print("off");  //scrivi
        }
        lcd.setCursor(7,1);  //posiziona il cursore a
        lcd.print(mode);  //scrivi lo stato del sistema
        lcd.setCursor(0,2);  //posiziona il cursore a
        lcd.print("Cold cycles:");  //scrivi
        lcd.setCursor(12,2);  //posiziona il cursore a
        lcd.print(cscount);  //scrivi il contatore delle accensioni ciclo raffreddamento
        lcd.setCursor(0,3);  //posiziona il cursore a
        lcd.print("Heat cycles:");  //scrivi
        lcd.setCursor(12,3);  //posiziona il cursore a
        lcd.print(hscount);  //scrivi il contatore delle accensioni ciclo riscaldamento
        printed=1;  //setta la variabile a 1 per visualizzare una sola volta
      }
    }
    if(act[0]!=0){  //condizione di avvio per evitare errori
      cputemptotal=0;  //resetta la somma dei valori
      for(int q=0;q<size5;q++){
        cputemptotal=cputemptotal+act[q];  //fai la somma dei valori rilevati
      }
      cputempaverage=cputemptotal/(float)size5;  //calcola la media dei valori rilevati
    }
  }
  //***ISTRUZIONE PER SPEGNERE ILLUMINAZIONE DISPLAY LCD***//
  if((long)(millis()-light)>=0){  //se tempo trascorso è maggiore del timer light
    lcd.setBacklight(LOW);
  }
  //***ISTRUZIONI PER SPEGNERE LA VENTILAZIONE***//
  if((long)(millis()-fan)>=0){  //se tempo trascorso è maggiore del timer fan
    ventilation=LOW;  //cambia lo stato della ventilazione in spento
    digitalWrite(5,ventilation);  //spegni la ventilazione
  }
  //***ISTRUZIONI PER ACCENDERE/SPEGNERE RAFFREDDAMENTO/RISCALDAMENTO***//
  if((long)(millis()-target)>=0){  //se tempo trascorso è maggiore del timer target
    if(refrigeration==HIGH||heating==HIGH){  //se il raffreddamento o il riscaldamento sono accesi
      if(refrigeration==HIGH){  //se il raffreddamento è acceso
        refrigeration=LOW;  //cambia lo stato del raffreddamento in spento
        digitalWrite(7,refrigeration);  //spegni il raffreddamento
        mode=3;  //setta il valore dello stato del sistema a 3
        target=millis()+cidle;  //imposta il timer di standby raffreddamento
        light=millis()+blon;  //imposta il timer di accensione display
      }
      else if(heating==HIGH){  //se il riscaldamento è acceso
        heating=LOW;  //cambia lo stato del riscaldamento in spento
        digitalWrite(8,heating);  //spegni il riscaldamento
        mode=4;  //setta il valore dello stato del sistema a 4
        target=millis()+hidle;  //imposta il timer di standby riscaldamento
        light=millis()+blon;  //imposta il timer di accensione display
      }
    }
    else{  //se il raffreddamento e il riscaldamento sono spenti
      if(fermtempaverage<maxtemp&&fermtempaverage>mintemp){  //se la temperatura media è compresa nell'intervallo impostato
        refrigeration=LOW;  //cambia lo stato del raffreddamento in spento
        digitalWrite(7,refrigeration);  //spegni il raffreddamento
        heating=LOW;  //cambia lo stato del riscaldamento in spento
        digitalWrite(8,heating);  //spegni il riscaldamento
        mode=5;  //setta il valore dello stato del sistema a 5
        target=millis()+sbidle;  //imposta il timer di standby generico
        light=millis()+blon;  //imposta il timer di accensione display
      }
      else if(fermtempaverage>maxtemp){  //se la temperatura media è superiore alla temperatura massima impostata
        refrigeration=HIGH;  //cambia lo stato del raffreddamento in acceso
        digitalWrite(7,HIGH);  //accendi il raffreddamento
        ventilation=HIGH;  //cambia lo stato della ventilazione in acceso
        digitalWrite(5,ventilation);  //accendi la ventilazione
        mode=1;  //setta il valore dello stato del sistema a 1
        target=millis()+cstartup;  //imposta il timer di startup raffreddamento
        light=millis()+blon;  //imposta il timer di accensione display
        fanon=cstartup+cidle;  //setta il tempo di accensione ventilazione per l intero ciclo
        fan=millis()+fanon;  //imposta il timer di accensione ventilazione camera di fermentazione
        fermtempaverage=99;  //setta il valore della temperatura media al massimo
        cscount++;  //aumenta il contatore accensioni
        if(cscount>99999999){  //se il contatore accensioni arriva a 100
          cscount=1;  //fai ripartire il contatore accensioni da 1
        }
      }
      else if(fermtempaverage<mintemp){  //se la temperatura media è inferiore alla temperatura minima impostata
        heating=HIGH;  //cambia lo stato del riscaldamento in acceso
        digitalWrite(8,heating);  //accendi il riscaldamento
        ventilation=HIGH;  //cambia lo stato della ventilazione in acceso
        digitalWrite(5,ventilation);  //accendi la ventilazione
        mode=2;  //setta il valore dello stato del sistema a 2
        target=millis()+hstartup;  //imposta il timer di startup riscaldamento
        light=millis()+blon;  //imposta il timer di accensione display
        fanon=hstartup+hidle;  //setta il tempo di accensione ventilazione per l intero ciclo
        fan=millis()+fanon;  //imposta il timer di accensione ventilazione camera di fermentazione
        fermtempaverage=-30;  //setta il valore della temperatura media al massimo
        hscount++;  //aumenta il contatore accensioni
        if(hscount>99999999){  //se il contatore accensioni arriva a 100
          hscount=1;  //fai ripartire il contatore accensioni da 1
        }
      }
    }
  }
  if(cputempaverage<=maxcputemp){  //se la temperatura media della centralina è inferiore alla temperatura massima impostata
    cpufan=0;  //setta la ventilazione a 0% pwm
    analogWrite(6,cpufan);  //spegni la ventilazione
  }
  else if(cputempaverage>maxcputemp&&cputempaverage<=(maxcputemp+(maxcputemp*1.05))){  //se la temperatura media della centralina è maggiore della temperatura massima impostata ma minore della temperatura massima impostata più 0,05%
    cpufan=84;  //setta la ventilazione a 33% pwm
    analogWrite(6,cpufan);  //accendi la ventilazione
  }
  else if(cputempaverage>(maxcputemp+(maxcputemp*1.05))&&cputempaverage<=(maxcputemp+(maxcputemp*1.10))){  //se la temperatura media della centralina è maggiore della temperatura massima impostata più 0,05% ma minore della temperatura massima impostata più 0,10%
    cpufan=168;  //setta la ventilazione a 66% pwm
    analogWrite(6,cpufan);  //accendi la ventilazione
  }
  else if(cputempaverage>(maxcputemp+(maxcputemp*1.10))&&cputempaverage<=(maxcputemp+(maxcputemp*1.15))){  //se la temperatura media della centralina è maggiore della temperatura massima impostata più 0,10% ma minore della temperatura massima impostata più 0,15%
    cpufan=255;  //setta la ventilazione a 100% pwm
    analogWrite(6,cpufan);  //accendi la ventilazione
  }
}
