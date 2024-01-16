1. Helmet

    Zabezpieczenia nagłówków HTTP: Używanie Helmet, zwłaszcza z funkcją frameguard({ action: "SAMEORIGIN" }), pomaga zabezpieczyć aplikację przed atakami typu Clickjacking, ograniczając możliwość osadzania strony w ramkach na innych domenach.
    
    Dla czego jest to ważne:

   - Zabezpieczenie przed atakami Clickjacking: Atak Clickjacking polega na osadzeniu zawartości strony internetowej w ramkach (iframe) na innej stronie, często złośliwej. W sytuacji, gdy strona jest osadzana na innej domenie bez zgody, może to prowadzić do różnych ataków, takich jak przechwytywanie danych, manipulowanie treścią lub fałszowanie akcji użytkownika.

   - Ograniczanie dostępu do ramki do tej samej domeny (SAMEORIGIN): Ustawiając frameguard({ action: "SAMEORIGIN" }), aplikacja ogranicza dostęp do swojej zawartości w ramkach tylko dla tych stron, które znajdują się w tej samej domenie. To skutecznie eliminuje potencjalne zagrożenia związane z osadzaniem strony na niezaufanych witrynach, chroniąc użytkowników przed atakami Clickjacking.

   - Ochrona integralności i bezpieczeństwa zawartości: Poprzez zastosowanie odpowiednich nagłówków HTTP, Helmet wspiera ochronę integralności i bezpieczeństwa zawartości serwowanej przez aplikację, co jest kluczowe dla zapewnienia bezpiecznego środowiska dla użytkowników.


