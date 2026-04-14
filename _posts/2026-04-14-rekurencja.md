---
title: Rekurencja - czym jest, przykłady
date: 2026-04-14
categories: [rekurencja, python]
tags: [rekurencja, python, koncepcje]
image: /assets/img/thumbnails/rekurencja.png
math: true
---

## Czym jest rekurencja?

Najprościej mówiąc, rekurencja to funkcja, która w swoim ciele wywołuje samą siebie. Jest to mechanizm używany w niektórych algorytmach, bądź mogący Nam zastąpić pętle (jednak musimy uważać, gdyż w bardziej skomplikowanych przypadkach może okazać się bardzo zasobożerny). Każde wykorzystanie rekurencji powinno być połączone z wystąpieniem w danej funkcji:

* Przypadku bazowego - czyli warunku, którego spełnienie oznacza zakończenie rekurencji
* Kroku rekurencyjnego - kroku, w którym funkcja wywołuje samą siebie, jednak podajemy jej inne argumenty, które przybliżają nas do przypadku bazowego.

## Jak działa stos wywołań w rekurencji

Stos wywołań działa jak wieża z talerzy: każde kolejne wywołanie funkcji rekurencyjnej odkłada nową „ramkę” z danymi na samą górę, wstrzymując działanie poprzednich etapów. Wszystkie te warstwy czekają w pamięci, dopóki program nie dotrze do przypadku bazowego, który pozwala „zdjąć” dane ze szczytu i przekazać wynik w dół. Jeśli rekurencja nie może dotrzeć do przypadku bazowego, dochodzi do przepełnienia stosu (gdyż zajmujemy za dużo miejsca w pamięci) - co skutuje wystąpieniem błędu przepełnienia stosu (StackOverflow).

## Najprostszy przykład rekurencji

Najprostszym przykładem rekurencji jest algorytm liczenia silni. <br>
Dla tych, którzy nie wiedzą co to silnia: [silnia - wikipedia](https://pl.wikipedia.org/wiki/Silnia)

Wzór na silnie:

$$n! = 1 \cdot 2 \cdot 3 \cdot \dots \cdot (n-1) \cdot n$$

W tym przypadku:

* Przypadek bazowy: n=0
* Krok rekurencyjny: aby policzyć 5! musimy policzyć wpierw 4!

Jak będzie wyglądał kod?

```python
def silnia(n):
    if n == 0:        # Przypadek bazowy
        return 1
    else:             # Krok rekurencyjny
        return n * silnia(n - 1)
```

## Inne przykłady zastosowania rekurencji

Odwracanie kolejności elementów w liście:

```python
def reverse_my_list(lista):
    if not lista:   # Przypadek bazowy - spawdzamy czy lista nie jest pusta
        return []
    else:
        return reverse_my_list(lista[1:]) + [lista[0]]   # Krok rekurencyjny
```

Oraz troche bardziej skomplikowany przykład - zliczanie unikalnych wartości w liście

```python
def count_uq_values(lista):
    # Przypadek bazowy - pusta lista ma 0 unikalnych elementów
    if not lista: 
        return 0
    
    head = lista[0]     # 1 element listy
    tail = lista[1:]    # Reszta elementów
    
    # Krok rekurencyjny
    if head in tail:
        return count_uq_values(tail)
    else:
        return 1 + count_uq_values(tail)
```

W tym przypadku z każdym wywołaniem przekazujemy do funkcji liste elementów pomniejszoną o 1 element (pierwszy z brzegu - head). W ten sposób funkcja dojdzie w końcu do wywołania, w którym przekazywana jej lista będzie pusta, czyli spełni się przypadek bazowy. Następnie nastąpi przekazanie wyników w góre stosu wywołań oraz złożenie wszystkiego w całość.

## Dlaczego warto używać rekurencji i na co należy uważać.

Rekurencja pozwala nam na rozbicie skomplikowanych problemów w serię mniejszych zadań. Jest dosyć schludna i idealnie sprawdza się w takich zadaniach jak:

* Przeszukiwanie struktur drzewopodobnych
* Implementowanie algorytmów (np. QuickSort)

Należy jednak mieć na uwadze, że jeżeli zapomnimy o przypadku bazowym to funkcja będzie się wywoływać tak długo, aż zabraknie nam pamięci - wystąpi wtedy błąd przepełnienia stosu.
