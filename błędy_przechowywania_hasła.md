NIEPRAWIDłOWE PRZECHOWYWANIE HASłA W BAZIE

W mechaniźmie weryfikacji rejestrowania została znaleziona podatność związane z hasłem.
Jest to **brak szyfrowania hasła** w bazie danych. Jest ono przechowywane jako String bez żadnuch zabezpieczeń.

    **Rekomendacje**: Zamiast przechowywać hasła w postaci czystego tekstu, użyta powinna zostać funkcja skrótu, np. bcrypt, do zabezpieczenia haseł. Funkcje skrótu są zaprojektowane tak, aby być trudnymi do odwrócenia, co oznacza, że nie można odzyskać oryginalnego hasła z zapisanego skrótu.
