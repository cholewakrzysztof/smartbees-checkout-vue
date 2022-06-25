## Project Setup


VUE
1.npm install
2.npm run dev

TESTY JEDNOSTKOWE PHPUNIT
1. Włączyć Git Bash
2. Prejść do folderu ze sklonowanym repozytorium "smart-bees-checkout-phpunit-poprawione"
3. komenda: vendor/bin/phpunit

WSPÓŁPRACA Z BAZĄ DANYCH
1. zaimportować bazę checkout.sql z nazwą "checkout"
2. wrzućić pliki "discount_check.php" oraz "send_order.php" tak, aby były dostęne pod ścieżkami:
    -"http://localhost/send_order.php"  
    -"http://localhost/discount_check.php" 

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
