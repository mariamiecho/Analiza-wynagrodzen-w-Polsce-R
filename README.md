## Wstęp

Regresja liniowa to metoda statystyczna, która modeluje zależność między zmienną objaśnianą (Y) a jedną lub wieloma zmiennymi objaśniającymi (X), przyjmując, że ta zależność ma postać liniową. Postać modelu regresji liniowej można zapisać wzorem: 
 
 Y=β0 +β1X+ε
 
gdzie 𝛽0 to wyraz wolny, 𝛽1 to parametr strukturalny, a 𝜀 to składnik losowy.

Za pomocą klasycznej metody najmniejszych kwadratów (KMNK) analizie zostanie poddany wpływ czynników na przeciętne miesięczne wynagrogrodzenie [zł] w Polsce z podziałem na wojewódstwa w roku 2019.



## Opis zmiennych

Y - przeciętne miesięczne wynagrodzenie [zł]

X1 - stopa bezrobocia [%]

X2 - średnia cena lokali mieszkalnych za m2 [tys. zł]

X3 - miesięczne wydatki na jedną osobę [tys. zł]

X4 - imigracja do nowego województwa [tys. os]

X5 - liczba zgłoszonych ofert pracy [tys. szt]

X6 - liczba aktywnie pracujących osób [tys.]

X7 - liczba nowo utworzonych miejsc pracy [tys.]

X8 - liczba zlikwidowanych miejsc pracy [tys.]



## Wymagabe biblioteki

```{r}
library(rio)
library(car)
library(lmtest)
```


## Importowanie danych:

```{r}
dane <- import("daneExcel.xlsx")
head(dane) # wyświetlenie danych
```


## Wstępny model liniowy

``` {r}
model <- lm( Y ~ X1 + X2 + X3 + X4 + X5 + X6 + X7 + X8, data = dane)
summary(model)
```
Zmienne X1, X3, X4, X5, X6, X7 i X8 nie są istotne statystycznie, oznacza to, że nie mają istotnego wpływu na zmienną objaśniającą. Należy wykonać regresję krokową wstecz aby usunąć z modelu nieistotne zmienne.


## Dobór zmiennych - regresja krokowa wstecz

krok 1, model regresji liniowej, gdzie zmienną objaśniającą jest Y: przeciętne miesięczne wynagrodzenie [zł]
 
``` {r}
model_1 <- lm( Y ~ X1 + X2 + X3 + X4 + X5 + X6 + X7 + X8, data = dane)
summary(model_1)
```

krok 2, nowy model regresji liniowej, bez zmiennej objaśniającej X4: imigracja do nowego województwa [tys. os]

```{r}
model_2 <- lm(Y ~ X1 + X2 + X3 + X5 + X6 + X7 + X8, data = dane)
summary(model_2)
```

krok 3,  nowy model regresji liniowej, bez zmiennej objaśniającej X4 i X6:liczba aktywnie pracujących osób [tys.]

```{r}
model_3 <- lm(Y ~ X1 + X2 + X3 + X5 + X7 + X8, data = dane)
summary(model_3)
```

krok 4, nowy model regresji liniowej, bez zmiennej objaśniającej X4, X6 i X5: liczba zgłoszonych ofert pracy [tys. szt]

``` {r}
model_4 <- lm(Y ~ X1 + X2 + X3 + X7 + X8, data = dane)
summary(model_4)
```

krok 5, nowy model regresji liniowej, bez zmiennej objaśniającej X4, X6, X5 i X1: stopa bezrobocia [%]
``` {r}
model_5 <- lm(Y ~ X2 + X3 + X7 + X8, data = dane)
summary(model_5)
```

krok 6, nowy model regresji liniowej, bez zmiennej objaśniającej X4, X6, X5, X1 i X7: liczba nowo utworzonych miejsc pracy [tys.]

``` {r}
model_6 <- lm(Y ~ X2 + X3 + X8, data = dane)
summary(model_6)
```
Wszystkie zmienne są istotne statystycznie. Nowy model zawiera 3 zmienne objaśniające: X2, X3 i X8.


## Identyfikacja obserwacji nietypowych 

Przed sprawdzeniem założeń klasycznej metody najmniejszych kwadratów należy upewnić się, że nie występują obserwacje nietypowe, które zniekształcają wyniki. Badany model zawiera niewielką grupę obserwacji - 16 wojewódstw.

```{r}
# k - ilość zmiennych objaśniających + 1 wyraz wolny
k <- length(coef(model_6))

# n - ilosc obserwacji
n <- nrow(dane)
```

1) Obserwacje wpływowe - Zniekształcają model. Usunięcie takiej obserwacji może zmienić oszacowania parametrów strukturalnych modelu.
```{r}
dffits <- dffits(model_6)
wplywowe <- which(abs(dffits) > 2*sqrt(k/n))
plot(dffits,
     main = "Wykres obserwacij wpływowych",
     xlab="",
     ylab="DFFITS",
     col = "blue",
     pch = 19);abline(h=0, col="purple")
```

W badanym modelu obserwacjami wpływowymi są obserwacje 7 i 12 czyli wojewódstwo mazowieckie i śląskie.

2) Obserwacje odstające - są to nietypowe wartości zmiennej objaśnianej (Y). Charakteryzują się dużymi wartościami reszt.
```{r}
rstud <- rstandard(model_6)
print(rstud)
odstajace <- which(abs(rstud) >= 2)
par(mfrow=c(1,2))
plot(rstud,
     main = "Wykres obserwacji odstających",
     col = "blue",
     pch = 19,
     xlab = "",
     ylab = "reszty");abline(h=0, col="purple")
boxplot(rstud, 
        main = "Wykres pudełkowy reszt")
```

W badanym modelu nie występują obserwacje odstające.

3) Obserwacje dźwigniowe - są to nietypowe wartości zmiennej objaśniającej (X).
```{r}
hatv <- hatvalues(model_6)
dzwigniowe <- which(hatv > 0.5)
barplot(hatv,
        col="lightblue",
        main = "Wykres obserwacji dźwigniowych",
        xlab = "Wojewódstwa",
        ylab = "hi"
);abline(h = 0.5, col="purple")
```

W badanym modelu obserwacjami dźwigniowymi są obserwacje 7 i 12 czyli wojewódstwo mazowieckie i śląskie.


## Współliniowość
Występuje gdy zmienne objaśniające są silnie skorelowane między sobą. 
Statystyka testwa: VIF = 1/(1-R^2 ).
Jeżeli VIF > 10 oznacza to, że występuje współliniowość, która obniża jakość modelu.
```{r}
vif(model_6)
```
Wszystkie zmienne objaśniające mają VIF < 10. Brak współliniowości.

## Istotność parametrów
Należy sprawdzić czy zmienne objaśniające są istotne statystycznie.
``` {r}
# H0: βj = 0    (parametr strukturalny nie jest statystycznie istotny)
# H1: βj =/= 0 (parametr strukturalny jest statystycznie istotny)
summary(model_6)
```
Dla każdej zmiennej p-value < 0.05 co oznacza, że odrzucamy H0 na rzecz H1. Zmienne są istotne statystycznie.

## Badanie normalności - test shapiro-wilka
``` {r}
# H0:	Składnik losowy modelu ma rozkład normalny
# H1:	Składnik losowy modelu nie ma rozkładu normalnego
reszty <- resid(model_6)
shapiro.test(reszty)
```

P-value > 0.05 co oznacza, że jest brak podstaw do odrzucenia H0. Badany model ma rozkład normalny.
```{r}
qqnorm(reszty,
       main = "Wykres rozkładu normalnego",
       col="blue",
       pch = 19);qqline(reszty)
```

## Badanie autokorelacji - test Durbina-Watsona
W modelu regresji liniowej autokorelacja reszt oznacza, że reszty są same ze sobą skorelowane.
```{r}
# H0: współczynnik autokorelacji ρ = 0 (występuje brak autokorelacji) 
# H1: współczynnik autokorelacji ρ > 0 (występuje autokorelacja dodatnia) 
durbinWatsonTest(model_6)
```
P-value > 0.05 oznacza brak podstaw do odrzucenia H0. Nie występuje autokorelacja reszt, nie są one od siebie zależne.


## Badanie homoskedastyczności - test Goldfelda-Quandta
Homoskedastyczność oznacza to, że wariancja reszt jest taka sama dla wszystkich wartości zmiennych niezależnych.
```{r}
# H0:	σ1^2=σ2^2	składnik losowy modelu jest homoskedastyczny	
# H1:	σ1^2 != σ2^2	składnik losowy modelu jest heteroskedastyczny	
gqtest(model_6)
```
P-value > 0.05 oznacza to brak podstaw do odrzucenia H0. Składnik losowy jest homoskedastyczny.

## Dopasowanie modelu do danych empirycznych
```{r}
summary(model_6)
```
Postać oszacowana modelu ekonometrycznego:

Ŷ= 2365,12 +121,91X2+809,12X3+ 14,7X8

Interpretacja:

- X2: Jeśli średnia cena lokali mieszkalnych za m2 (x2) wzrośnie w danym województwie o tysiąc złotych, to przeciętne miesięczne wynagrodzenie (y) w tym wojewódzwie wzrośnie średnio o 121.91zł przy założeniu, że miesięczne wydatki na jedną osobę i liczba zlikwidowanych miejsc pracy się nie zmienią.

- X3: Jeżeli średnie miesięczne wydatki na jedną osobę (x3)  w danym wojewódzwie wzrosną o tysiąc złotych  to przeciętne miesięczne wynagrodzenie (y) w tym wojewódzwie wzrośnie średnio o 809.12zł przy założeniu, że średnia cena lokali mieszkalnych za m2 i liczba zlikwidowanych miejsc pracy się nie zmienią.

- X8: Jeżeli liczba zlikwidowanych miejsc pracy (x8) w danym województwie wzrośnie o tysiąc to przeciętne miesięczne wynagordzenie (y) w tym wojewódzwie wzrośnie średnio o 14,70zł przy założeniu, że średnia cena lokali mieszkalnych za m2 oraz miesięczne wydatki na jedną osobę nie zmienią się.

- Współczynnik determinacji R^2: 92,4% całkowitej zmienności miesięcznego wynagrodzenia zostało wyjaśnione modelem.

- Współczynnik korelacji R: korelacja między miesięcznym wynagrodzeniem a ceną lokali mieszkalnych za m2, miesięcznymi wydatkami a zlikwidowanymi miejscami pracy jest bardzo silna.
- Odchylenie standardowe Se: Wartość empiryczna różni się średnio o 148,35zł na osobę od wartości teoretycznych.

## Wnioskowanie
Na podstawie przedstawionych informacji oraz przeprowadzonej analizy można stwierdzić, że przeciętne wynagrodzenie w regionie zależy od różnych czynników ekonomicznych, demograficznych i rynkowych. Jednym z nich jest średnia cena mieszkań, która odzwierciedla poziom rozwoju gospodarczego oraz atrakcyjność regionu. Wyższe ceny mieszkań często wiążą się z wyższymi kosztami życia, co wywołuje presję na wzrost płac. Dodatkowo, takie regiony zazwyczaj charakteryzują się obecnością lepiej wynagradzanych sektorów gospodarki.

Istotnym czynnikiem są także miesięczne wydatki na osobę, które obrazują koszty życia. W zamożniejszych regionach, gdzie wydatki są wyższe, wynagrodzenia są zwykle dostosowywane, aby zapewnić mieszkańcom odpowiedni standard życia.

Liczba zlikwidowanych miejsc pracy również wpływa na wynagrodzenia. Gdy redukcje dotyczą sektorów o niskich płacach, przeciętne wynagrodzenie może wzrosnąć, ponieważ dominują lepiej opłacane stanowiska.
