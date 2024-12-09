
#include <Wire.h>          // Communication I²C pour le RTC DS1307
#include <RTClib.h>        // Bibliothèque pour le RTC DS1307
#include <LedControl.h>    // Contrôle des matrices MAX7219

// Initialisation du module RTC
RTC_DS1307 rtc;

// Initialisation des modules MAX7219 (DIN = 12, CLK = 11, LOAD = 10)
LedControl lc1 = LedControl(12, 11, 10, 1);  // Matrice 1
LedControl lc2 = LedControl(12, 11, 10, 1);  // Matrice 2
LedControl lc3 = LedControl(12, 11, 10, 1);  // Matrice 3
LedControl lc4 = LedControl(12, 11, 10, 1);  // Matrice 4

void setup() {
  // Initialisation de la communication série pour le débogage
  Serial.begin(9600);

  // Initialisation du module RTC
  if (!rtc.begin()) {
    Serial.println("Erreur : Module RTC introuvable !");
    while (1); // Blocage si le RTC n'est pas détecté
  }

  if (!rtc.isrunning()) {
    Serial.println("RTC arrêté, initialisation en cours...");
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__))); // Réglage de l'heure à l'heure actuelle
  }

  // Initialisation des modules MAX7219
  lc1.shutdown(0, false);  // Active la matrice 1
  lc1.setIntensity(0, 8);  // Réglage de la luminosité (0 à 15)
  lc1.clearDisplay(0);     // Efface la matrice 1

  lc2.shutdown(0, false);  // Active la matrice 2
  lc2.setIntensity(0, 8);  // Réglage de la luminosité (0 à 15)
  lc2.clearDisplay(0);     // Efface la matrice 2

  lc3.shutdown(0, false);  // Active la matrice 3
  lc3.setIntensity(0, 8);  // Réglage de la luminosité (0 à 15)
  lc3.clearDisplay(0);     // Efface la matrice 3

  lc4.shutdown(0, false);  // Active la matrice 4
  lc4.setIntensity(0, 8);  // Réglage de la luminosité (0 à 15)
  lc4.clearDisplay(0);     // Efface la matrice 4
}

void loop() {
  // Lecture de l'heure actuelle depuis le RTC
  DateTime now = rtc.now();

  // Extraction des heures et minutes
  int hour = now.hour();
  int minute = now.minute();

  // Affichage de l'heure sur les matrices
  displayTime(hour, minute);

  // Pause d'une seconde avant mise à jour
  delay(1000);
}

// Fonction pour afficher l'heure sur les matrices MAX7219
void displayTime(int hour, int minute) {
  // Conversion des heures et minutes en format HH:MM
  int digits[4] = {
    hour / 10,     // Chiffre des dizaines des heures
    hour % 10,     // Chiffre des unités des heures
    minute / 10,   // Chiffre des dizaines des minutes
    minute % 10    // Chiffre des unités des minutes
  };

  // Affichage des chiffres sur chaque matrice
  lc1.setDigit(0, 0, digits[0], false); // Matrice 1 : Dizaines des heures
  lc2.setDigit(0, 0, digits[1], false); // Matrice 2 : Unités des heures
  lc3.setDigit(0, 0, digits[2], false); // Matrice 3 : Dizaines des minutes
  lc4.setDigit(0, 0, digits[3], false); // Matrice 4 : Unités des minutes
}
