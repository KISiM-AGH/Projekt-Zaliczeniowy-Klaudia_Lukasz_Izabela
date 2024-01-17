## Użycie MD5 do haszowania hasła

### poziom ryzyka:
średni

### opis:
Używanie MD5 do haszowania haseł jest obecnie uznawane za niebezpieczne, ponieważ łatwo jest złamać tę funkcję haszującą przy użyciu tzw. ataków "rainbow table".

### Warunki niezbędne do wykorzystania podatności
[Opis]

### Szczeguły techniczne
MD5 generuje stałą długość skrótu, co sprawia, że atakiujący mogą skonstruować tzw. "rainbow tables" (tęczowe tablice) - pre-obliczone zestawy haseł i ich skrótów, umożliwiające szybkie odgadnięcie oryginalnego hasła na podstawie skrótu.

### Lokalizacja
vulnerable-JAVA/src/main/java/com/fruit/servlet
/AdminServlet.java

### Rekomendacja
Zastąpienie MD5 bardziej bezpiecznym algorytmem haszującym, na przykład bcrypt.
