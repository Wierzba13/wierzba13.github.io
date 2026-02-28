---
title: Git - konfiguracje oraz podstawowe polecenia
date: 2026-02-28
categories: [Git]
tags: [git, github]
image: /assets/img/thumbnails/git.png
---

## Konfiguracja

Po zainstalowaniu Gita musimy przeprowadzić jeszcze krótką, podstawową konfiguracje, jeżeli natomiast nie zainstalowałeś jeszcze Gita, możesz go pobrać z tego linku: [Git](https://git-scm.com/install/).

Po instalacji otwórz terminal i wpisz następujące komendy, które ustawią twój adres email (taki jak na koncie GitHub) oraz imię i nazwisko. Będą one widoczne w historii commitów.

```bash
git config --global user.name "imie nazwisko"
git config --global user.email "twoj@email.com"
```

## Tworzenie repozytorium lokalnego oraz śledzenie zmian

Aby stworzyć repozytorium lokalne przechodzimy do naszego folderu, który stanie się naszym repo. Następnie inicjalizujemy repozytorium (tworzy się katalog `.git/`) poprzez polecenie `git init`. Aby sprawdzić status naszego repozytorium używamy polecenia `git status`. Teraz, kiedy udało nam się stworzyć repozytorium lokalne, możemy wskazywać, które pliki mają być uwzględniane przy następnych zapisach.

Aby dodać plik do obszaru oczekiwania (z którego później tworzy się commity):

```bash
git add <nazwa_pliku>
git add <plik1> <plik2> <plik3>
git add .
git add *.html
```

Pliki możemy wskazywać na różne sposoby, przy czym użycie kropki `.` oznacza dodanie wszystkich plików - poza tymi znajdującymi się w specjalnym pliku `.gitignore` (katalog główny repozytorium). Pliki się tam znajdujące są ingorowane przez system Git i nie będą śledzone.

Kiedy mamy już pliki w obszarze oczekiwania, możemy dokonać commita - czyli takiego zapisu aktualnego stanu plików znajdujących się w obszarze oczekiwania (coś na wzór zapisu postępu w grze).

```bash
git commit -m "Opis zmian"
git commit
```

Jeżeli nie użyjemy flagi `-m` Git otworzy nam edytor, który wybraliśmy przy instalacji, aby móc opisać naszego commita (o tym jak zrobić to poprawnie mówię później).

W momencie kiedy w naszym projekcie dokonaliśmy kilku commitów, możemy chcieć podejrzeć ich historię, w tym celu używamy `git log`:

```bash
git log                     # wyświetla pełną historię commitów
git log --oneline           # prezentuje skróconą historię w jednej linii
git log --grep="test"       # wyszukuje commity zawierające w opisie słowo "test"
```

## Porządkowanie plików i naprawa błędów

Git daje nam tu duża kontrolę za pomocą odpowiednich poleceń.

Usuwanie plików z obszaru oczekiwania (odwrotność polecenia `git add`)

```bash
git reset <nazwa_pliku>
git reset
```

Przywracanie działajacej wersji pliku

```bash
git checkout <nazwa_pliku>
```

Git wyciąga ostatnią zapisaną wersję danego pliku i zastępuje nią wersję aktualną. Można to zrobić również dla całego projektu cofając się do konkretnego commita:

```bash
git checkout <hash>
```

Gdzie hash to hash przypisany do interesującego nas commita, możemy go znaleźć dzięki poleceniu `git log`. Poniżej przykładowy output:

```bash
commit <hash> (HEAD -> main, origin/main, origin/HEAD)
Author: Raul Wierzbiński <Wierzba@tuta.io>              # Dane które konfigurowalismy na samym początku
Date:   <date>

    <describtion>
```

Istnieje też opcja przywrócenia konkretnego pliku z konkretnego commitu:

```bash
git checkout <hash> <nazwa_pliku>
```

TODO
clean,

checkout jako switch itp

## Revert vs Reset

Polecenia te służa do naprawy błędów przeszłości, poniżej porównanie:

|   Cecha   |                `git revert`                |             `git reset`              |
| :-------: | :----------------------------------------: | :----------------------------------: |
| Działanie |      Tworzy nowy commit "odwracający"      |    Cofa wskaźnik projektu do tyłu    |
| Historia  | Zachowuje cała historię, nie usuwa niczego | Usuwa commity z historii (ryzykowne) |

Skupmy się więc na `git revert`, jak ono działa?

* Wszystko, co stało się wcześniej, zostaje w logach. Wiesz dokładnie, kto coś zepsuł i kto to naprawił.
* Nie zmieniasz historii, co ułatwia synchronizację kodu
* Możesz cofnąć commit sprzed tygodnia, nie dotykając zmian, które nastąpiły po nim (o ile nie ma konfliktów w tych samych plikach).

Składnia polecenia:

```bash
git revert <hash>
```


## Branche

Branch to gałąź projektu, na którym rozwijasz daną funkcjonalność, a następnie scalasz go z branchem głównym (zazwyczaj main). Podstawowe operacje na branchach wyglądają następująco.

Tworzenie nowego brancha i przełączenie się na niego
```bash
git checkout -b <nazwa>
```

Przełączanie się między istniejącymi branchami
```bash
git checkout <nazwa>
```

Usuwanie brancha
```bash
git branch -D <nazwa>
```

Łączenie dwóch branchy
```bash
git merge <nazwa>
```

Działa to tak, że łączysz branch `<nazwa>` z branchem na którym aktualnie się znajdujesz. Warto wiedzieć, że mogą tu wystąpić konflikty, które trzeba rozwiązać.

## Merge vs Rebase

Merge jest popularnym rozwiązaniem łączenia dwóch branchy, gdy pracuje nad nimi wiele osób. Integruje on zmiany z jednego brancha do drugiego (najczęściej z brancha gdzie implementujemy jakąś funkcjonalność do brancha main).

```bash
# Wracamy do brancha main
git checkout main

# Scalamy zmiany z brancha <nazwa>
git merge <nazwa>
```

Git tutaj stworzy niemal zawsze nowego commita (takiego zbiorczego, nazywanego `merge commit`), aby połączyć zmiany z obydwu gałęzi. Jest to świetne rozwiązanie do przechowywania szczegółowej historii, ale może skutkować złożoną, trudną do analizy i nieliniową historią commitów.

Natomast Rebase działa nieco inaczej, ponieważ zamiast tworzyć `merge commit`, tworzy on liniową historię commitów

```bash
# Przenosimy się na brancha naszej funkcjonalności
git checkout <nazwa>

git rebase main
```

Źródło i więcej informacji [Merge vs Rebase](https://www.frontstack.pl/blog/git-merge-vs-rebase)

## Praca z repozytorium zdalnym

Repozytorium zdalne to odpowiednik naszego repozytorium lokalnego na jakiejś chmurze lub serwerze, do którego przesyłamy zmiany aby inni mogli z nich korzystać.

Aby podpiąć repozytorium zdalne:

```bash
git remote add origin <URL>
```

Gdzie `origin` jest dla nas aliasem odnoszącym się do naszego `<URL>`.

Wysyłanie zmian do repozytorium zdalnego (muszą być poddane commitowi)

```bash
git push -u origin main
```

Flaga `-u` pozwala zapamiętać adres, następnym razem wystarczy że użyjemy polecenia:

```bash
git push
```

Pobieranie zmian z zdalnego repozytorium:

```bash
git fetch     # Pobiera zmiany ale nie łączy ich z Twoim kodem

git pull      # Pobiera zmiany i łączy je z Twoim kodem
```

## Jak profesjonalnie połączyć 2 branche?

1. Zaciągnij najnowsze zmiany: 
```bash
git pull
```

2. Stworzenie nowego brancha i praca na nim: 
```bash
git checkout -b <nazwa_brancha>
```

3. Dodawanie i commitowanie zmian na tym branchu
4. Wysłanie brancha do repozytorium zdalnego: 
```bash
git push origin <nazwa_brancha>
```

5. Gdy skończysz prace, "wyrównaj" swoją gałąź do aktualnego stanu brancha main, aby uniknąć konfliktów przy merge'u. Flaga `--rebase` pozwala nam na natychmiastowe wykonanie rebase Twoich lokalnych commitów na te pobrane z main
```bash
git pull --rebase origin main
```

6. Wyślij zmiany
```bash
git push origin <nazwa_brancha> --force-with-lease
```

7. Łączenie (Squash Merge) - zamienia wiele małych commitów w jeden konkretny
```bash
git checkout main
git merge --squash <nazwa_brancha>
git commit
git push origin main
```

8. Usunięcie niepotrzebnego już brancha

```bash
git branch -D <nazwa_brancha>                   # Usunięcie brancha lokalnie

git push origin --delete <nazwa_brancha>        # Usunięcie brancha na serwerze
```

## Jak prawidłowo opisywać commita?

Opis zmian jest kluczowym aspektem, aby przyszły Ty lub twoi współpracownicy mogli szybko zrozumieć co się działo w danym commicie. Używając komendy `git commit` Git otwiera nam edytor, który wybraliśmy podczas instalacji, dzieki czemu możemy wygodnie opisać naszego commita. Warto się tu trzymać paru zasad:

1. ***Tytuł*** - piszemy z dużej litery, bez kropki
2. ***Pusty wiersz*** - rozdziela tytuł oraz opis
3. ***Opis*** - Opisujący zwięźle zmiany w trybie rozkazującym

Warto również wspomnieć, że używamy tu języka angielskiego !!!

```bash
Tytuł

Opis...
```

## Podsumowanie

Git to potężne narzędzie a najlepszym sposobem na jego opanowanie jest po prostu korzystanie z niego oraz poszukanie potrzebnych Nam informacji. Warto pamiętać, że to są tylko i wyłącznie podstawowe komendy, których opanowanie otwiera drzwi do dalszego zagłębiania się w tym narzędziu.
