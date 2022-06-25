## Project Setup


VUE
1. git clone https://github.com/cholewakrzysztof/smartbees-checkout-vue-poprawione
2. cd smartbees-checkout-vue-poprawione
3. npm install 
4. npm run dev

TESTY JEDNOSTKOWE PHPUNIT
1. git clone https://github.com/cholewakrzysztof/smart-bees-checkout-phpunit-poprawione
2. do tego samego folderu skopiować plik "send_order.php"
3. Włączyć Git Bash
4. cd smart-bees-checkout-phpunit-poprawione
5. vendor/bin/phpunit

WSPÓŁPRACA Z BAZĄ DANYCH
1. zaimportować bazę checkout.sql z nazwą "checkout"
2. wrzućić pliki "discount_check.php" oraz "send_order.php" tak, aby były dostępne pod ścieżkami:
3. "http://localhost/send_order.php"  
4. "http://localhost/discount_check.php" 

Domeny reCAPTCHA:
    smartbees-zadanie.local
    localhost
Klucz reCAPTCHA:
    6Lfxfl0gAAAAAOIupaOkUkeTBFhY2qGXxyv8MVsJ

W sekcji <script> -> methods,na samym końcu została zakomentowana metoda wypełniająca formularz przykładowymi danymi
W pierwszej linii <template> został zakomentowany przycisk do aktywacji metody wypełniającej formularz

Kody rabatowe (Wpływają jedynie na cenę produktu):
    Aktywny: AB-123-456
    Nieaktywny: CD-789-123

Wykorzystane technologie:
1. Vue.js 3
2. Baza danych MariaDB
3. PHP 7.3.30
4. reCAPTCHA Google v3
5. PHPUnit 9
