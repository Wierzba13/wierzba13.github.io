---
title: Podstawy Programowania Obiektowego (OOP) w Javie
date: 2026-10-29
categories: [OOP, java]
tags: [oop, backend, poradnik, java]
image: /assets/img/thumbnails/java_oop.jpg
---

## Czym jest OOP

Object Oriented Programming, to programowanie skupiające się na pisaniu skalowalnych i łatwych w utrzymaniu aplikacji.
Zamiast pisać luźny ciąg instrukcji, organizujemy kod wokół obiektów, które posiadają swój stan i zachowanie. Ułatwia to późniejsze rozszerzanie kodu o nowe funkcjalności oraz pomaga zachować porządek.

Mówi się, że głównymi filarami OOP są:

* ***Abstrakcja*** - ukrywanie złożonych szczegółów implementacyjnych za prostym interfejsem oraz skupienie się wyłącznie na cechach obiektu istotnych z punktu widzenia aktualnego problemu.
* ***Enkapsulacja*** - ukrywanie stanu wewnętrznego obiektu oraz jego danych przed niepowołanym dostępem oraz ich kontrolowane modyfikowanie.
* ***Dziedziczenie*** - mechanizm pozwalający tworzyć nowe klasy na bazie już istniejących, co umożliwia ponowne wykorzystanie kodu i tworzenie logicznych hierarchii typu rodzic-dziecko.
* ***Polimorfizm*** - zdolność traktowania obiektów różnych klas pochodnych jako obiektów jednego typu bazowego, gdzie to samo wywołanie metody uruchamia różną logikę w zależności od rzeczywistego typu ukrytego pod spodem obiektu.

## Klasa i obiekt

`Klasa` to zdefiniowany przez nas szablon z danymi atrybutami i metodami. Natomiast `obiekt` to konkretna instancja klasy, posiadająca konkretne informacje.

```java
// Klasa / Szablon
public class User {
    // Atrybuty (stan)
    private String login;
    private String password;

    // Metody (zachowanie)
    public void sayHello() {
        System.out.println("Witaj w serwisie " + this.login + " !");
    }
}

// Obiekt
User u1 = new User()
```

## Metody i słowo kluczowe this

W Javie niezwykle ważnym elementem pracy z metodami i atrybutami klasy jest słowo kluczowe this. Jest to specjalna referencja, która zawsze wskazuje na `obecny obiekt` – czyli na tę konkretną instancję klasy, wewnątrz której aktualnie się znajdujemy.

```java
public class User {
    private String name;

    // Konstruktor (nazwa argumentu jest taka sama jak nazwa pola)
    public User(String name) {
        // this.name odnosi się do pola w klasie
        // name (bez this) to parametr przekazany w nawiasach
        this.name = name; 
    }

    // Metoda wykonująca akcję (zachowanie)
    public void introduceYourself() {
        System.out.println("Cześć, nazywam się " + this.name);
    }
}
```


## Modyfikatory dostępu i Enkapsulacja

Enkapsulacja to proces ukrywania danych, tak aby nie były one niepotrzebnie widoczne poza klasą. Ma to na celu ograniczenie błędów i niechcianych modyfikacji. W Javie do tego celu służą modyfikatory dostępu, których możemy używać na klasach, metodach, atrybutach itp.

| Modyfikator dostępu                  | W tym samym pakiecie | W klasie dziedziczącej (inny pakiet) | Gdziekolwiek (reszta świata) |
| :----------------------------------- | :------------------: | :----------------------------------: | :--------------------------: |
| **`public`**                         |        ✔️ Tak         |                ✔️ Tak                 |            ✔️ Tak             |
| **`protected`**                      |        ✔️ Tak         |                ✔️ Tak                 |            ❌ Nie             |
| **Brak (default / package-private)** |        ✔️ Tak         |                ❌ Nie                 |            ❌ Nie             |
| **`private`**                        |        ❌ Nie         |                ❌ Nie                 |            ❌ Nie             |

## Konstruktory, Gettery i Settery

Skoro wiemy już, że klasa ma swoje pola, które mogą być `private` to musimy mieć jakąś możliwość ich kontrolowania - tj. przypisywania im wartości oraz odczytu. Pierwszą z takich opcji jest użycie `konstruktora`. Jest to specjalna metoda, która nazywa się dokładnie tak jak klasa oraz jest wywoływana w momencie utworzenia obiektu danej klasy za pomocą słowa kluczowego ***new***. Java dla każdej klasy tworzy automatycznie konstruktor domyślny - jednak możemy definiować również własne.

W celu modyfikacji i odczytu danych z pól prywatnych tworzy się również tzw. gettery i settery:
* ***settery*** służą do ustawiania wartości danch atrybutów
* ***gettery*** zwracają przechowywane przez nie wartości

Metody te mają określone nazewnictwo (jak w przykładzie niżej), aby wiedzieć do którego pola się odnoszą oraz co robią (set lub get). Settery przyjmują jako argument wartość jaką chcemy ustawić i nic nie zwracają, natomiast gettery zwracają nam odpowiednie wartości.
Warto również zaznaczyć, że settery nie powinny dopisywać wartości czy modyfikować atrybut w dany sposób - służa one do przypisania wartości. Do innych operacji należy zaimplementować odpowiednie metody logiki biznesowej.

```java
public class BankAccount {
    private double balance;

    // Konstruktor
    public BankAccount(double initialBalance) {
        if (initialBalance >= 0) {
            this.balance = initialBalance;
        } else {
            this.balance = 0;
        }
    }

    // Getter
    public double getBalance() {
        return balance;
    }

    // Setter z walidacją
    public void setBalance(double balance) {
        if (balance > 0) {
            this.balance = balance;
        }
    }
}
```

Warto tu zaznaczyć, że bez ustawiania atrybutów klasy jako prywatnych moglibyśmy wykonać prosty odczyt oraz przypisanie bez potrzeby pisania dodatkowego kodu. Jednak w takim wypadku nie mielibyśmy kontroli nad przypisywaniem wartości, co nie ma miejsca przy używaniu setterów, w których możemy dokonywać potrzebnej walidacji i innych operacji.

## Przeciążanie metod i konstruktorów

W poprzednim punkcie mówiłem, że setter przyjmuje jako argument odpowiednią wartość jak w przykładzie poniżej:

```java
public void setBalance(double balance) {
    if (balance > 0) {
        this.balance = balance;
    }
}
```

`double balance` jest tu argumentem metody. W javie mamy jednak możliwość tworzenia metody o tej samej nazwie z różną listą argumentów. W takiej sytuacji JVM sama wybierze odpowiednią wersje.

```java
public class NotificationService {
    public void sendNotification(String message) {
        System.out.println("Wysyłam e-mail: " + message);
    }

    // Przeciążona metoda
    public void sendNotification(String message, String phoneNumber) {
        System.out.println("Wysyłam SMS na numer " + phoneNumber + ": " + message);
    }
}
```

Przeciążać możemy również konstruktory

```java
public class User {
    private String username;
    private String email;
    private boolean isActive;

    // Główny konstruktor przyjmujący wszystkie parametry
    public User(String username, String email, boolean isActive) {
        this.username = username;
        this.email = email;
        this.isActive = isActive;
    }

    // Konstruktor tylko z nazwą użytkownika (zakłada domyślne wartości)
    public User(String username) {
        // Wywołuje główny konstruktor powyżej za pomocą this(...)
        this(username, "brak@email.com", true);
    }

    // onstruktor bezargumentowy
    public User() {
        this("Anon", null, false);
    }
}

User user1 = new User("JanKowalski", "jan@test.pl", true); // Wywoła konstruktor nr 1
User user2 = new User("AnnaNowak");                        // Wywoła konstruktor nr 2
User user3 = new User();                                   // Wywoła konstruktor nr 3
```

## Dziedziczenie

Dziedziczenie pozwala jednej klasie (dziecku) przejąć pola i metody innej klasy (rodzica). Pozwala to na unikanie duplikacji kodu oraz tworzenie hierarchii w kodzie. Używamy do tego słowa kluczowego `extends`. Jeśli chcemy wywołać metodę lub konstruktor z klasy nadrzędnej, używamy słowa `super`.

Przykładowo `Dog` dziedziczy po ogólnej klasie `Animal`:

```java
public class Animal {
    private String name;

    public Animal(String name) {
        this.name = name;
    }

    // Metoda, która zostanie odziedziczona
    public void makeSound() {
        System.out.println("Zwierzę wydaje niezidentyfikowany dźwięk...");
    }

    public String getName() {
        return name;
    }
}

public class Dog extends Animal {
    private String breed; // Pole specyficzne tylko dla psa

    public Dog(String name, String breed) {
        // super() wywołuje konstruktor klasy bazowej (Animal). 
        // Musi to być pierwsza instrukcja w konstruktorze!
        super(name); 
        this.breed = breed;
    }

    // Adnotacja @Override informuje kompilator, że nadpisujemy metodę rodzica
    @Override
    public void makeSound() {
        // Używamy getName(), ponieważ pole 'name' w Animal jest private
        System.out.println(getName() + " szczeka: Hau hau!");
    }

    // Metoda dostępna tylko dla obiektów typu Dog
    public void fetch() {
        System.out.println(getName() + " (rasa: " + breed + ") przynosi patyk!");
    }
}
```

## Abstrakcja oraz Polimorfizm - czyli interfejsy oraz klasy abstrakcyjne

Wiesz już, jak ukrywać stan obiektu (hermetyzacja) oraz jak przejmować cechy innych klas (dziedziczenie). Czas na dwa brakujące filary, które nadają programowaniu obiektowemu prawdziwą elastyczność: Abstrakcję i Polimorfizm. W Javie realizujemy je najczęściej za pomocą klas abstrakcyjnych oraz interfejsów.

### Abstrakcja 

Abstrakcja polega na ukryciu skomplikowanych szczegółów implementacyjnych i pokazaniu światu zewnętrznemu tylko tego, co niezbędne. Zamiast interesować się tym, jak dana czynność jest wykonywana pod spodem, skupiamy się na tym, co ma zostać zrobione. W Javie do tworzenia abstrakcji używamy przede wszystkim:

* ***Interfejsów (`interface`)*** - o swoiste kontrakty. To zbiory pustych metod (bez ciał), które klasa "zgadzająca się" na ten kontrakt musi zaimplementować (nadpisać).
* ***Klas Abstrakcyjnych (`abstract class`)*** - to hybrydy. Mogą posiadać zarówno gotowe metody z logiką (jak zwykłe klasy), jak i metody abstrakcyjne (puste), które muszą zostać zaimplementowane przez klasy pochodne. Ważne: nie można utworzyć instancji (obiektu) klasy abstrakcyjnej.

```java
// Próba wywołania: new Employee("Jan") zwróci błąd kompilacji.
public abstract class Employee {
    // Klasa abstrakcyjna może posiadać zwykłe pola (stan)
    private String name;

    // Klasa abstrakcyjna może posiadać konstruktor (wywoływany przez klasy pochodne za pomocą super())
    public Employee(String name) {
        this.name = name;
    }

    // Zwykła metoda - gotowa logika współdzielona i dostępna dla wszystkich klas pochodnych
    public void printDetails() {
        System.out.println("Pracownik: " + this.name);
    }

    // Metoda abstrakcyjna - nie ma ciała. Każda klasa dziedzicząca MUSI dostarczyć jej własną implementację.
    public abstract double calculateSalary();
}

public class Manager extends Employee {
    private double monthlySalary;

    public Manager(String name, double monthlySalary) {
        // Obowiązkowe wywołanie konstruktora klasy nadrzędnej (abstrakcyjnej)
        super(name); 
        this.monthlySalary = monthlySalary;
    }

    // Kompilator wymusza na nas nadpisanie tej metody i dostarczenie logiki
    @Override
    public double calculateSalary() {
        return this.monthlySalary;
    }
}
```

### Polimorfizm

Polimorfizm ściśle współpracuje z abstrakcją i dziedziczeniem. Pozwala nam traktować obiekty różnych, konkretnych klas tak, jakby były obiektami jednego (wspólnego) typu bazowego – np. wspólnego interfejsu. Dzięki temu możemy pisać uniwersalny kod, gdzie to samo wywołanie metody włącza inną logikę w zależności od "prawdziwego" typu obiektu.

```java
// Abstrakcja: Tworzymy interfejs (kontrakt), który określa CO ma się stać
public interface PaymentMethod {
    // Metoda abstrakcyjna - deklarujemy co ma robić, ale nie jak
    void pay(double amount); 
}

// Używamy słowa kluczowego 'implements' do podpięcia interfejsu
public class BlikPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        System.out.println("Przetwarzanie płatności BLIK na kwotę: " + amount + " PLN");
    }
}

public class CardPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        System.out.println("Pobieranie środków z karty kredytowej: " + amount + " PLN");
    }
}

// --------------------- Uruchomienie przykładu ---------------------

public class OrderService {
    // Magia polimorfizmu: metoda przyjmuje DOWOLNY obiekt, 
    // który implementuje PaymentMethod
    public void processOrder(PaymentMethod paymentMethod, double amount) {
        paymentMethod.pay(amount); // JVM w locie zdecyduje, której implementacji użyć
    }
}

OrderService orderService = new OrderService();

// Polimorfizm - referencja typu PaymentMethod wskazuje na konkretny obiekt BLIK lub Card
PaymentMethod myBlik = new BlikPayment();
PaymentMethod myCard = new CardPayment();

// Ten sam kod, różne zachowania!
orderService.processOrder(myBlik, 150.0); // Wypisze: Przetwarzanie płatności BLIK...
orderService.processOrder(myCard, 50.0);  // Wypisze: Pobieranie środków z karty...
```

### Dlaczego jest to takie ważne?

Gdy w przyszłości zechcesz dodać do aplikacji inną metodę płatności (np. kryptowalutami), nie będziesz musiał w ogóle dotykać ani modyfikować klasy OrderService. Wystarczy, że utworzysz nową klasę implementującą PaymentMethod, a reszta systemu przyjmie ją bez problemu.

## Pola współdzielone oraz stałe - static oraz final

`static` to słowo kluczowe, które oznacza pole lub metode w klasie jako przynależną do klasy a nie jej obiektów. Oznacza to tylko tyle, że takie pole/metodę współdzielą ze sobą wszystkie instancje danej klasy.

```java
public class UserAccount {
    // Zwykłe pole - każdy użytkownik ma własne imię
    private String username; 
    
    // Pole statyczne - współdzielone przez WSZYSTKIE obiekty UserAccount
    private static int totalUsersCount = 0; 

    public UserAccount(String username) {
        this.username = username;
        totalUsersCount++; // Zwiększamy globalny licznik przy każdym nowym obiekcie
    }

    // Metoda statyczna - wywołujemy ją na klasie, nie na obiekcie
    public static int getTotalUsers() {
        return totalUsersCount;
    }
}

UserAccount user1 = new UserAccount("Jan");
UserAccount user2 = new UserAccount("Anna");
System.out.println("Zarejestrowanych: " + UserAccount.getTotalUsers());
```

`final` pozwala nam zdefiniować stałą (czyli nie możemy zmienić jej wartości, jeżeli raz dokonamy przypisania). Możemy również użyć tego słowa kluczowego dla klasy - nie będziemy mieli wtedy możliwości dziedziczenia po tej klasie (nie będzie się jej dało rozszerzyć).

```java
public class Configuration {
    // Po przypisaniu w konstruktorze, wartości nie można zmienić
    private final String dbUrl;

    public Configuration(String dbUrl) {
        this.dbUrl = dbUrl;
    }
}
```

## Immutable Objects - obiekty, których nie da się modyfikować

Obiekt "immutable" to taki, którego stan nie może ulec żadnej zmianie po jego utworzeniu. W programowaniu backendowym (i pracy z bazami danych czy wielowątkowością) są one niezwykle pożądane, ponieważ eliminują błędy związane z przypadkową modyfikacją danych.
Aby stworzyć taki obiekt w Javie:

* Klasa musi być `final`
* Wszystkie pola muszą być `final private`
* Pola inicjalizowane są wyłącznie przez `konstruktor`
* Brak setterów (tylko gettery do odczytu)

```java
public final class UserCredentials {
    private final String username;
    private final String passwordHash;

    public UserCredentials(String username, String passwordHash) {
        this.username = username;
        this.passwordHash = passwordHash;
    }

    public String getUsername() {
        return username;
    }

    public String getPasswordHash() {
        return passwordHash;
    }
}
```
