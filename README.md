## Setup


[VUE]
1. git clone https://github.com/cholewakrzysztof/smartbees-checkout-vue-poprawione
2. cd smartbees-checkout-vue-poprawione
3. npm install 
4. npm run dev

[TESTY JEDNOSTKOWE PHPUNIT]
1. git clone https://github.com/cholewakrzysztof/smart-bees-checkout-phpunit-poprawione
2. do tego samego folderu skopiować plik "send_order.php"
3. Włączyć Git Bash
4. cd smart-bees-checkout-phpunit-poprawione
5. vendor/bin/phpunit

[WSPÓŁPRACA Z BAZĄ DANYCH]
1. zaimportować bazę checkout.sql z nazwą "checkout"
2. wrzućić pliki "discount_check.php" oraz "send_order.php" tak, aby były dostępne pod ścieżkami:
3. "http://localhost/send_order.php"  
4. "http://localhost/discount_check.php" 

## Inne iformacje
Domeny reCAPTCHA:
1. smartbees-zadanie.local
2. localhost

Klucz reCAPTCHA:
1. 6Lfxfl0gAAAAAOIupaOkUkeTBFhY2qGXxyv8MVsJ

## Wskazówki
1. W sekcji <script> -> methods,na samym końcu została zakomentowana metoda wypełniająca formularz przykładowymi danymi
2. W pierwszej linii <template> został zakomentowany przycisk do aktywacji metody wypełniającej formularz
3. Kody rabatowe 
 1. Aktywny: AB-123-456
 2. Nieaktywny: CD-789-123
 
 Inne iformacje

-Vue.js 3
-Baza danych MariaDB
-PHP 7.3.30
-reCAPTCHA Google v3
-PHPUnit 9
