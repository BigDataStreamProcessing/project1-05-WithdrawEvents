# Charakterystyka danych

Dane dotyczą wypłat z bankomatów  

W strumieniu pojawiają się zdarzenia zgodne ze schematem `WithdrawEvent`.

```
create json schema WithdrawEvent(cardnumber string, cardtype string, amount int, city string, country string, ets string, its string);
```

Pojedynczym zdarzeniem w systemie jest operacja wypłaty z bankomatu.
Dane w zdarzeniu zawierają informacje na temat karty (numer i typ), ilości wypłaconej gotówki, miejsca wypłaty (miasto i kraj). 

Dane uzupełnione są o dwie etykiety czasowe. 
* Pierwsza (`ets`) związana jest z momentem dokonania wypłaty. 
  Etykieta ta może się losowo spóźniać w stosunku do czasu systemowego maksymalnie do 30 sekund.
* Druga (`its`) związana jest z momentem rejestracji zdarzenia dokonania wypłaty w systemie.

# Opis atrybutów

Atrybuty w każdym zdarzeniu zgodnym ze schematem `WithdrawEvent` mają następujące znaczenie:
* `cardnumber ` - numer karty płatniczej
* `cardtype` - rodzaj karty płatniczej (np. visa, american_express)
* `amount` - wypłacona kwota
* `city` - miasto, w którym doszło do wypłaty
* `country` - państwo, w którym doszło do wypłaty
* `ets` - czas, w którym dokonano wypłaty
* `its` - czas rejestracji faktu dokonania wypłaty w systemie

# Zadania
Opracuj rozwiązania poniższych zadań. 
* Opieraj się strumieniu zdarzeń zgodnych ze schematem `WithdrawEvent`
* W każdym rozwiązaniu możesz skorzystać z jednego lub kilku poleceń EPL.
* Ostatnie polecenie będące ostatecznym rozwiązaniem zadania musi 
  * być poleceniem `select` 
  * posiadającym etykietę `answer`, przykładowo:

```aidl
@name('answer') SELECT cardnumber, amount 
FROM WithdrawEvent#ext_timed(java.sql.Timestamp.valueOf(its).getTime(), 3 sec)
```

## Zadanie 1
Dla każdego rodzaju karty płatniczej utrzymuj informację dotyczącą sumy wypłaconej gotówki zarejestrowanej w ciągu ostatnich 30 sekund.

Wyniki powinny zawierać, następujące kolumny:
- `cardtype` - rodzaj karty 
- `sumamount` - suma wypłat zarejestrowanych w ciągu ostatnich 30 sekund

## Zadanie 2
Wykrywaj przypadki, gdy wypłacona gotówka przekracza kwotę 900.

Wyniki powinny zawierać, następujące kolumny:
- `cardnumber` - numer karty
- `amount` - wypłacona kwota
- `ets` - czas wypłacenia gotówki

## Zadanie 3
Wykrywaj przypadki, gdy wypłacona gotówka jest o co najmniej 50% większa od średniej ilości gotówki w ramach zarejestrowanych wypłat dla tego samego rodzaju kart w ciągu ostatnich 2 minut.

Wyniki powinny zawierać, następujące kolumny:
- `cardnumber`- numer karty
- `cardtype` - rodzaj karty
- `amount` - wypłacona kwota
- `avgamount` - średnia wypłaconej gotówki w ciągu ostatnich 2 minut

## Zadanie 4
Dla każdego z dwóch rodzajów kart (visa, american_express) utrzymuj listę nazw miejscowości wraz z sumą wartości wypłaconych pieniędzy oraz liczbą wypłat zarejestrowanych w ciągu ostatnich 10 sekund. 

Prezentuj zestawienie dotyczące miast, które pojawiają się na każdej z powyższych list.

Wyniki powinny zawierać, następujące kolumny:
- `city` - miasto
- `number_visa` - liczba wypłat dla kart visa
- `sumamount_visa` - wypłacona sumaryczna kwota dla kart visa
- `number_american_express` - liczba wypłat dla kart american_express
- `sumamount_american_express` - wypłacona sumaryczna kwota dla kart american_express

## Zadanie 5
Wyszukuj serie wypłat (z co najmniej dwie) z kwotą powyżej 900 zakończonych wypłatą o kwocie poniżej 900. Wypłaty w serii muszą pojawić się w systemie w ciągu 60 sekund.

Zadbaj o to, aby wynikowe serie nie nakładały się na siebie. 

Wyniki powinny zawierać, następujące kolumny:
- `amount` - kwota pierwszej wypłaty rozpoczynającej serię
- `ets` - czas pierwszej wypłaty rozpoczynającej serię


## Zadanie 6
Uwzględniając tylko wypłaty dla kart typ 'maestro' znajduj serię trzech wypłat występujących po sobie (nie koniecznie bezpośrednio) z kwotą powyżej 100. Interesują nas tylko takie serie, w których pomiędzy pierwszą a ostatnią wypłatą serii nie nie nastąpiła wypłata z karty o typie 'visa'.

Zadbaj o to, aby wynikowe serie nie nakładały się na siebie. 

Wyniki powinny zawierać, następujące kolumny:
- `a_amount` - kwota pierwszej wypłaty
- `b_amount` - kwota drugiej wypłaty
- `c_amount` - kwota trzeciej wypłaty

## Zadanie 7
Dla każdego miasta znajduj serię co najmniej 5 wypłat, w trakcie których kwota każdej wypłaty była większa od wypłaty poprzedniej. Seria ma się kończyć wypłatą o kwocie mniejszej niż wypłata poprzedzająca.

Zadbaj o to, aby wynikowe serie nie nakładały się na siebie. 

Wyniki powinny zawierać, następujące kolumny:
- `how_many` - ilość wypłat w serii wzrostowej 
- `city` - nazwa miasta
- `minimum` - pierwsza wypłata we wzrostowej serii
- `maximum` - ostatnia wypłata we wzrostowej serii