## Setup


[VUE]
1. git clone https://github.com/cholewakrzysztof/smartbees-checkout-vue-poprawione
2. cd smartbees-checkout-vue-poprawione
3. npm install 
4. npm run dev

[TESTY JEDNOSTKOWE PHPUNIT]
1. git clone https://github.com/cholewakrzysztof/smart-bees-checkout-phpunit-poprawione
2. do folderu nadrzędnego skopiować plik "send_order.php" lub zmienić tests/FormValidationTest.php linia :22
3. włączyć Git Bash
4. cd smart-bees-checkout-phpunit-poprawione
5. vendor/bin/phpunit

[WSPÓŁPRACA Z BAZĄ DANYCH]
1. stworzyć nową bazę danych o nzawie "checkout" system kodowania utf8_general_ci
1. zaimportować bazę checkout.sql
2. wrzućić pliki "discount_check.php" oraz "send_order.php" tak, aby były dostępne pod ścieżkami lub zmienić w kodzie:
3. "http://localhost/send_order.php" lub src/components/CheckOut.vue linia :198
5. "http://localhost/discount_check.php"  lub src/components/CheckOut.vue linia :150
6. włączyć serwer MySQL

## Inne informacje
Domeny reCAPTCHA:
1. smartbees-zadanie.local
2. localhost

Klucz reCAPTCHA:
1. 6Lfxfl0gAAAAAOIupaOkUkeTBFhY2qGXxyv8MVsJ

## Wskazówki
1. W pliku src/components/CheckOut.vue linia:341 została zakomentowana metoda wypełniająca formularz przykładowymi danymi
2. W pliku src/components/CheckOut.vue linia:369 został zakomentowany przycisk do aktywacji metody wypełniającej formularz i akceptującej reCaptcha

[Kody rabatowe] 
 1. Aktywny: AB-123-456
 2. Nieaktywny: CD-789-123
 
[Wykorzystane technologie]

1. Vue.js 3
2. Baza danych MariaDB
3. PHP 7.3.30
4. reCAPTCHA Google v3
5. PHPUnit 9
