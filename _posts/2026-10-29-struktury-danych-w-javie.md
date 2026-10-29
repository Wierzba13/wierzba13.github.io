---
title: Struktury danych w Javie
date: 2026-10-29
categories: [java]
tags: [java, data]
image: /assets/img/thumbnails/struktury_danych_java.png
math: true
---


## Struktura danych

Struktura danych to określony sposób organizacji, przechowywania oraz zarządzania danymi. Jej odpowiedni dobór może być kluczowym czynnikiem w wymagających operacjach, a o jej doborze decydują trzy kluczowe aspekty:

* ***Wydajność***, czyli inaczej złożoność obliczeniowa operacji. Właściwa struktura sprawi, że wyszukiwanie miliona rekordów zajmie ułamek sekundy podczas gdy inna struktura może spowolnić ten proces do nawet kilku minut!
* ***Zużycie pamięci*** - niektóre struktury rezerwują cały blok pamięci z góry, a inne dynamicznie alokują pamięć w miare zapotrzebowania
* ***Przechowywanie*** - niektóre struktury nie pozwalają przykładowo na przechowywanie duplikatów, inne przechowują pary klucz-wartość itp.

## Tablice

Tablice w Javie to podstawowe struktury danych przechowujące z góry określoną liczbę elementów tego samego typu w ciągłym obszarze pamięci. Ich największą zaletą jest błyskawiczny dostęp do dowolnego elementu na podstawie jego indeksu, co gwarantuje stałą złożoność czasową $O(1)$. Główną wadą jest natomiast brak elastyczności, ponieważ raz zainicjalizowanej tablicy nie można już dynamicznie powiększać ani pomniejszać.

```java
public class Example {
    public static void main(String[] args) {
        // Tablica 4 elementów typu String
        String[] owoce = new String[4];

        // Przypisanie wartości do poszczególnych indexów (indexujemy od 0!!!)
        owoce[0] = "Jabłko";
        owoce[1] = "Banan";
        owoce[2] = "Pomarańcza";
        owoce[3] = "Mango";

        // Iteracja po tablicy
        for (String owoc : owoce) {
            System.out.println(owoc);
        }
    }
}
```

## ArrayList oraz LinkedList

Listy rozwiązują główny problem zwykłych tablic – potrafią dynamicznie zmieniać swój rozmiar. W Javie najczęściej korzystamy z dwóch implementacji interfejsu `List`, które oferują ten sam zestaw metod, ale drastycznie różnią się swoim działaniem pod spodem.

* ***ArrayList*** oparta jest na tablicach - używa ona zwykłej tablicy, która automatycznie powiększa (tworząc nową, większą i kopiując do niej zawartość) gdy brakuje jej miejsca. Zapewnia to błyskawiczny odczyt (z racji użycia tablic) kosztem wydajności podczas wstawiania i usuwania elementów.
* ***LinkedList*** to dwukierunkowa struktura węzłów. Elementy nie leżą obok siebie w pamięci - każdy z nich przechowuje wskaźnik do poprzedniego i następnego elementu. Odczyt po indexie jest wolny ze względu na potrzebę przeskakiwania po węzłach, ale same operacje usuwania i wstawiania są błyskawiczne (wystarczy przepięcie dwóch referencji).

```java
import java.util.ArrayList;

public class Example {
    public static void main(String[] args) {
        
        ArrayList<String> allowedIps = new ArrayList<>();
        
        // Zwykłe dodanie na koniec listy
        allowedIps.add("192.168.1.10");
        allowedIps.add("192.168.1.15");
        
        // "Insert" pod konkretny indeks
        // Wstawia nowy adres na pozycję 1, co wymusza przesunięcie "192.168.1.15" o jedno miejsce w prawo.
        allowedIps.add(1, "10.0.0.5"); 
        
        // Sprawdzenie, czy element istnieje
        boolean isWhitelisted = allowedIps.contains("10.0.0.5");
        System.out.println("Czy adres jest na białej liście? " + isWhitelisted);

        // Sprawdzenie indeksu konkretnego elementu
        int index = allowedIps.indexOf("192.168.1.15");
        System.out.println("Adres .15 znajduje się pod indeksem: " + index);
        
        // Wypisanie całości, aby zobaczyć ostateczną kolejność
        System.out.println("Pełna lista: " + allowedIps);

        // Iteracja po liście
        for (int i = 0; i < allowedIps.size(); i++) {
            System.out.println("Pozycja " + i + ": " + allowedIps.get(i));
        }
    }
}
```

## Zbiory (Sety)

Zbiór to struktura danych, której głównym zadaniem jest przechowywanie unikalnych elementów. Jeśli spróbujesz dodać do zbioru wartość, która już w nim istnieje, operacja ta zostanie po prostu zignorowana. W Javie najpopularniejszą implementacją zbioru jest `HashSet`, który pod spodem wykorzystuje hashowanie, aby błyskawicznie sprawdzać czy dany element znajduje się już w kolekcji. Jego wadą jest brak zachowania kolejności elementów.

```java
import java.util.HashSet;

public class Example {
    public static void main(String[] args) {
        
        HashSet<String> uniqueVisitors = new HashSet<>();

        // Symulacja wejść na stronę (np. z logów serwera)
        uniqueVisitors.add("192.168.1.10"); // Pierwsze wejście
        uniqueVisitors.add("10.0.0.55");    // Ktoś inny
        
        // Ten sam użytkownik odświeża stronę - Set po prostu to zignoruje
        uniqueVisitors.add("192.168.1.10"); 

        System.out.println("Liczba unikalnych gości: " + uniqueVisitors.size()); // Wynik: 2

        // Sprawdzenie na żywo z wykorzystaniem zwracanej wartości (boolean)
        String newIp = "10.0.0.55";
        boolean isFirstVisit = uniqueVisitors.add(newIp);
        
        if (!isFirstVisit) {
            System.out.println("Odrzucam prośbę. Adres " + newIp + " już pobrał dzisiaj ten plik.");
        }
    }
}
```

## Mapy (klucz->wartość)

Mapa (Map) to struktura, która całkowicie różni się od list i zbiorów (nie dziedziczy po interfejsie Collection). Służy do przechowywania danych w parach `Key->Value`. Każdy klucz w mapie musi być unikalny, ale wartości mogą się powtarzać.Najbardziej uniwersalną implementacją jest HashMap. Pozwala ona powiązać ze sobą dwa obiekty i wyszukiwać je z ekstremalnie wysoką wydajnością.

```java
import java.util.HashMap;

public class Example {
    public static void main(String[] args) {
        
        // Klucz (Login gracza) -> Wartość (Liczba punktów)
        HashMap<String, Integer> playerScores = new HashMap<>();

        // Dodawanie nowych par (metoda put)
        playerScores.put("janek88", 150);
        playerScores.put("ania_pro", 300);

        // Aktualizacja danych (ten sam klucz po prostu nadpisuje wartość)
        // Janek zdobywa 50 punktów, więc nadpisujemy jego wynik
        playerScores.put("janek88", 200); 

        // Błyskawiczny odczyt na podstawie klucza
        int aniaScore = playerScores.get("ania_pro");
        System.out.println("Punkty Ani: " + aniaScore); // Wynik: 300

        // Przydatny trik: getOrDefault
        // Zamiast pisać if'y sprawdzające czy gracz istnieje, możemy zwrócić wartość domyślną
        int guestScore = playerScores.getOrDefault("nieznany_gracz", 0);
        System.out.println("Punkty gościa: " + guestScore); // Wynik: 0
        
        // Sprawdzenie, czy klucz istnieje w ogóle w mapie
        if (playerScores.containsKey("janek88")) {
            System.out.println("Janek jest już zarejestrowany w systemie punktacji.");
        }
    }
}
```

## Kolejka (FIFO) oraz Stos (LIFO)

Kolejka działa na zasadzie FIFO - czyli First In, First Out. Można to przyrównać do kolejki w sklepie - kto pierwszy wszedł, zostanie obsłużony (wyjdzie) również jako pierwszy.
Natomiast Stos działa na zasadzie LIFO - Last In, First Out. Przypomina to stos talerzy w restauracji - ten który został dołożony do stosu jako ostatni, zostanie pobrany do mycia jako pierwszy.


```java
import java.util.ArrayDeque;

public class Example {
    public static void main(String[] args) {
        
        // ArrayDeque to obecnie najwydajniejsza standardowa kolejka w Javie
        ArrayDeque<String> orderQueue = new ArrayDeque<>();

        // offer() - Klienci składają zamówienia, które trafiają na koniec kolejki
        orderQueue.offer("Zamówienie #101: Laptop Dell");
        orderQueue.offer("Zamówienie #102: Myszka bezprzewodowa");
        orderQueue.offer("Zamówienie #103: Monitor 4K");

        System.out.println("Zamówienia oczekujące na realizację: " + orderQueue.size());

        // peek() - Kto jest pierwszy w kolejce? (tylko odczyt)
        System.out.println("Następne w kolejności jest: " + orderQueue.peek());
        System.out.println("--- Rozpoczynamy pakowanie ---");

        // poll() - Pobieramy zamówienie z przodu i USUWAMY je z kolejki
        // Pętla wykonuje się tak długo, aż kolejka nie będzie pusta
        while (!orderQueue.isEmpty()) {
            String currentOrder = orderQueue.poll();
            System.out.println("Pakowanie i wysyłka: " + currentOrder);
        }
        
        System.out.println("Wszystkie zamówienia zrealizowane. Kolejka jest pusta!");
    }
}
```

```java
import java.util.ArrayDeque;

public class Example {
    public static void main(String[] args) {
        
        // Zastosowanie: Śledzenie historii nawigacji (np. przycisk "Wstecz" w przeglądarce)
        // Używamy ArrayDeque jako nowoczesnej implementacji stosu w Javie
        ArrayDeque<String> historyStack = new ArrayDeque<>();

        // push() - Dokładanie elementu na sam szczyt stosu
        historyStack.push("Strona Główna");
        historyStack.push("Kategoria: Laptopy");
        historyStack.push("Produkt: MacBook Pro");

        System.out.println("Aktualnie jesteś na: " + historyStack.peek()); // MacBook Pro

        // pop() - Ściągnięcie elementu ze szczytu
        // Użytkownik klika "Wstecz", więc usuwamy aktualną stronę i pobieramy poprzednią.
        historyStack.pop(); 
        
        String previousPage = historyStack.peek();
        System.out.println("Cofnięto do: " + previousPage); // Kategoria: Laptopy
    }
}
```

## Porównanie wydajności

| Struktura Danych    | Odczyt (Access)            | Wyszukiwanie (Search)                 | Wstawianie (Insert)                              | Usunięcie (Delete)                               |
| :------------------ | :------------------------- | :------------------------------------ | :----------------------------------------------- | :----------------------------------------------- |
| **Tablica (Array)** | $O(1)$ po indeksie         | $O(n)$                                | $O(n)$ (wymaga skopiowania do nowej)             | $O(n)$ (wymaga przesunięcia elementów)           |
| **ArrayList**       | $O(1)$ po indeksie         | $O(n)$                                | $O(1)$ na koniec / $O(n)$ w środku               | $O(n)$ (wymaga przesunięcia elementów)           |
| **LinkedList**      | $O(n)$                     | $O(n)$                                | $O(1)$ na krańcach / $O(1)$ po znalezieniu węzła | $O(1)$ na krańcach / $O(1)$ po znalezieniu węzła |
| **HashSet**         | *Brak dostępu po indeksie* | $O(1)$ po wartości                    | $O(1)$ (zamortyzowane)                           | $O(1)$                                           |
| **HashMap**         | $O(1)$ po kluczu           | $O(1)$ po kluczu / $O(n)$ po wartości | $O(1)$ (zamortyzowane)                           | $O(1)$ po kluczu                                 |
| **ArrayDeque**      | *Brak dostępu po indeksie* | $O(n)$                                | $O(1)$ na krańcach (amortyzowane)                | $O(1)$ na krańcach                               |
