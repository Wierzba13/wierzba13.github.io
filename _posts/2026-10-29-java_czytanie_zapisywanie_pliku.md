---
title: Zapisywanie i odczytywanie plików w Javie
date: 2026-10-29
categories: [java]
tags: [java, io]
image: /assets/img/thumbnails/io_java.jpg
---

## Odczyt niewielkich plików z użyciem klasy Scanner

```java
import java.nio.file.Path;
import java.util.Scanner;
import java.io.IOException;

public class Example {
    public static void loadFile(Path path) {
        try (Scanner scanner = new Scanner(path)) {
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                System.out.println(line);
            }
        } catch (IOException e) {
            throw new RuntimeException("Nie udało się odczytać pliku", e);
        }
    }
}
```

## Zapis do pliku linia po lini

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.io.PrintWriter;
import java.io.IOException;

public class Example {
    public void writeFile(Path path) {
        try (PrintWriter writer = new PrintWriter(Files.newBufferedWriter(path))) {
            for (int i = 1; i <= 5; i++) {
                // Zapisuje tekst i automatycznie przechodzi do nowej linii
                writer.println("To jest linia numer: " + i);
            }
        } catch (IOException e) {
            System.out.println("Błąd zapisu: " + e.getMessage());
        }
    }
}
```

## Dopisywanie do pliku

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.io.PrintWriter;
import java.io.IOException;

public class Example {
    public void appendToFile(Path path) {
        try (PrintWriter writer = new PrintWriter(Files.newBufferedWriter(
                path, StandardOpenOption.CREATE, StandardOpenOption.APPEND))) {
            
            for (int i = 6; i <= 8; i++) {
                writer.println("To jest nowa, dopisana linia numer: " + i);
            }   
        } catch (IOException e) {
            System.out.println("Błąd dopisywania: " + e.getMessage());
        }
    }
}
```

## Odczyt dużych plików - Strumienie oraz klasa BufferedReader

Aby przy przetwarzaniu dużych plików nie napotkać na problemy z wydajnością lub co gorsza ma błąd typu `OutOfMemoryError` musimy przetwarzać dane kawałek po kawałku.

### Strumienie

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.io.IOException;
import java.util.stream.Stream;

public class Example {
    public void processLargeFile(Path path) {
        // Używamy try-with-resources, aby automatycznie zamknąć strumień i zwolnić plik
        try (Stream<String> lines = Files.lines(path)) {

            lines.filter(line -> line.contains("ERROR"))
                    .forEach(System.out::println);
                 
        } catch (IOException e) {
            System.err.println("Błąd podczas przetwarzania: " + e.getMessage());
        }
    }
}
```

### BufferedReader

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.io.BufferedReader;
import java.io.IOException;

public class Example {
    public void processLargeFile(Path path) {
        try (BufferedReader reader = Files.newBufferedReader(path)) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            System.err.println("Błąd odczytu: " + e.getMessage());
        }
    }
}
```

## Zapis dużych plików - BufferedWriter

Gdy generujesz ogromny raport w pętli, ciągłe uderzanie do dysku twardego metodą writeString drastycznie spowolni aplikację. Z pomocą przychodzi BufferedWriter, który zbiera dane w pamięci (w buforze) i zapisuje je na dysku w większych, optymalnych paczkach.

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.io.BufferedWriter;
import java.io.IOException;
import java.util.List;

public class Example {
    public void generateReport(List<String> largeDataSet, Path path) {
        // try-with-resources automatycznie zamknie writer i zrzuci (flush) resztę bufora na dysk
        try (BufferedWriter writer = Files.newBufferedWriter(path)) {
            for (String record : largeDataSet) {
                writer.write(record);
                writer.newLine(); // Dopisuje poprawny znak nowej linii dla danego systemu operacyjnego
            }
        } catch (IOException e) {
            System.err.println("Błąd generowania raportu: " + e.getMessage());
        }
    }
}
```
