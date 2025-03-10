## Wstęp

Regresja liniowa to metoda statystyczna, która modeluje zależność między
zmienną objaśnianą (Y) a jedną lub wieloma zmiennymi objaśniającymi (X),
przyjmując, że ta zależność ma postać liniową. Postać modelu regresji
liniowej można zapisać wzorem:

Y=β0 +β1X+ε

gdzie 𝛽0 to wyraz wolny, 𝛽1 to parametr strukturalny, a 𝜀 to składnik
losowy.

Za pomocą klasycznej metody najmniejszych kwadratów (KMNK) analizie
zostanie poddany wpływ czynników na przeciętne miesięczne
wynagrogrodzenie \[zł\] w Polsce z podziałem na wojewódstwa w roku 2019.

## Opis zmiennych

Y - przeciętne miesięczne wynagrodzenie \[zł\]

X1 - stopa bezrobocia \[%\]

X2 - średnia cena lokali mieszkalnych za m2 \[tys. zł\]

X3 - miesięczne wydatki na jedną osobę \[tys. zł\]

X4 - imigracja do nowego województwa \[tys. os\]

X5 - liczba zgłoszonych ofert pracy \[tys. szt\]

X6 - liczba aktywnie pracujących osób \[tys.\]

X7 - liczba nowo utworzonych miejsc pracy \[tys.\]

X8 - liczba zlikwidowanych miejsc pracy \[tys.\]

## Wymagabe biblioteki

    library(rio)

    ## Warning: package 'rio' was built under R version 4.4.2

    library(car)

    ## Warning: package 'car' was built under R version 4.4.2

    ## Loading required package: carData

    ## Warning: package 'carData' was built under R version 4.4.2

    library(lmtest)

    ## Warning: package 'lmtest' was built under R version 4.4.3

    ## Loading required package: zoo

    ## Warning: package 'zoo' was built under R version 4.4.3

    ## 
    ## Attaching package: 'zoo'

    ## The following objects are masked from 'package:base':
    ## 
    ##     as.Date, as.Date.numeric

## Importowanie danych:

    dane <- import("daneExcel.xlsx")
    head(dane) # wyświetlenie danych

    ##                nazwa       Y  X1    X2      X3     X4      X5   X6   X7   X8
    ## 1       DOLNOŚLĄSKIE 5323.55 3.5 4.552 2.29215  6.986 159.493 5206 51.5 22.5
    ## 2 KUJAWSKO-POMORSKIE 4494.37 4.3 4.074 2.05987  7.751  75.039 3642 23.8 12.1
    ## 3          LUBELSKIE 4564.85 5.3 4.782 1.84928 10.047  49.210 3586 27.1 10.9
    ## 4           LUBUSKIE 4559.96 1.9 3.185 2.02860  4.490  46.881 1737 12.3  5.7
    ## 5            ŁÓDZKIE 4790.10 3.9 4.236 2.23217  7.687 123.293 4459 54.6 23.3
    ## 6        MAŁOPOLSKIE 5098.26 3.3 6.458 1.79865  7.064  91.582 5766 76.4 32.9

## Wstępny model liniowy

    model <- lm( Y ~ X1 + X2 + X3 + X4 + X5 + X6 + X7 + X8, data = dane)
    summary(model)

    ## 
    ## Call:
    ## lm(formula = Y ~ X1 + X2 + X3 + X4 + X5 + X6 + X7 + X8, data = dane)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -170.755  -87.840    7.219   73.730  220.478 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept) 2919.09366  745.97530   3.913   0.0058 **
    ## X1           -51.53983   63.63397  -0.810   0.4446   
    ## X2           166.48550   69.27439   2.403   0.0472 * 
    ## X3           509.30196  350.16297   1.454   0.1891   
    ## X4             3.01885   46.30245   0.065   0.9498   
    ## X5             2.33941    2.63112   0.889   0.4035   
    ## X6            -0.05705    0.12244  -0.466   0.6554   
    ## X7           -16.74835   16.60227  -1.009   0.3467   
    ## X8            53.53083   31.99135   1.673   0.1382   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 165 on 7 degrees of freedom
    ## Multiple R-squared:  0.9452, Adjusted R-squared:  0.8826 
    ## F-statistic:  15.1 on 8 and 7 DF,  p-value: 0.0009058

Zmienne X1, X3, X4, X5, X6, X7 i X8 nie są istotne statystycznie,
oznacza to, że nie mają istotnego wpływu na zmienną objaśniającą. Należy
wykonać regresję krokową wstecz aby usunąć z modelu nieistotne zmienne.

## Dobór zmiennych - regresja krokowa wstecz

krok 1, model regresji liniowej, gdzie zmienną objaśniającą jest Y:
przeciętne miesięczne wynagrodzenie \[zł\]

    model_1 <- lm( Y ~ X1 + X2 + X3 + X4 + X5 + X6 + X7 + X8, data = dane)
    summary(model_1)

    ## 
    ## Call:
    ## lm(formula = Y ~ X1 + X2 + X3 + X4 + X5 + X6 + X7 + X8, data = dane)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -170.755  -87.840    7.219   73.730  220.478 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept) 2919.09366  745.97530   3.913   0.0058 **
    ## X1           -51.53983   63.63397  -0.810   0.4446   
    ## X2           166.48550   69.27439   2.403   0.0472 * 
    ## X3           509.30196  350.16297   1.454   0.1891   
    ## X4             3.01885   46.30245   0.065   0.9498   
    ## X5             2.33941    2.63112   0.889   0.4035   
    ## X6            -0.05705    0.12244  -0.466   0.6554   
    ## X7           -16.74835   16.60227  -1.009   0.3467   
    ## X8            53.53083   31.99135   1.673   0.1382   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 165 on 7 degrees of freedom
    ## Multiple R-squared:  0.9452, Adjusted R-squared:  0.8826 
    ## F-statistic:  15.1 on 8 and 7 DF,  p-value: 0.0009058

krok 2, nowy model regresji liniowej, bez zmiennej objaśniającej X4:
imigracja do nowego województwa \[tys. os\]

    model_2 <- lm(Y ~ X1 + X2 + X3 + X5 + X6 + X7 + X8, data = dane)
    summary(model_2)

    ## 
    ## Call:
    ## lm(formula = Y ~ X1 + X2 + X3 + X5 + X6 + X7 + X8, data = dane)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -171.085  -88.748    6.639   73.843  217.212 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept) 2945.24761  588.49643   5.005  0.00105 **
    ## X1           -49.18927   49.06406  -1.003  0.34543   
    ## X2           164.15240   55.50205   2.958  0.01821 * 
    ## X3           500.05946  299.59657   1.669  0.13365   
    ## X5             2.37295    2.41443   0.983  0.35448   
    ## X6            -0.05274    0.09645  -0.547  0.59944   
    ## X7           -17.01230   15.06574  -1.129  0.29153   
    ## X8            53.86846   29.53948   1.824  0.10567   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 154.3 on 8 degrees of freedom
    ## Multiple R-squared:  0.9452, Adjusted R-squared:  0.8972 
    ## F-statistic: 19.71 on 7 and 8 DF,  p-value: 0.0001894

krok 3, nowy model regresji liniowej, bez zmiennej objaśniającej X4 i
X6:liczba aktywnie pracujących osób \[tys.\]

    model_3 <- lm(Y ~ X1 + X2 + X3 + X5 + X7 + X8, data = dane)
    summary(model_3)

    ## 
    ## Call:
    ## lm(formula = Y ~ X1 + X2 + X3 + X5 + X7 + X8, data = dane)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -202.714  -80.644    9.438   78.382  216.749 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 2818.419    519.367   5.427 0.000418 ***
    ## X1           -54.474     46.191  -1.179 0.268509    
    ## X2           170.744     52.024   3.282 0.009499 ** 
    ## X3           539.755    279.118   1.934 0.085154 .  
    ## X5             1.914      2.174   0.880 0.401555    
    ## X7           -19.547     13.765  -1.420 0.189313    
    ## X8            51.825     28.138   1.842 0.098624 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 148.2 on 9 degrees of freedom
    ## Multiple R-squared:  0.9431, Adjusted R-squared:  0.9052 
    ## F-statistic: 24.88 on 6 and 9 DF,  p-value: 4.053e-05

krok 4, nowy model regresji liniowej, bez zmiennej objaśniającej X4, X6
i X5: liczba zgłoszonych ofert pracy \[tys. szt\]

    model_4 <- lm(Y ~ X1 + X2 + X3 + X7 + X8, data = dane)
    summary(model_4)

    ## 
    ## Call:
    ## lm(formula = Y ~ X1 + X2 + X3 + X7 + X8, data = dane)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -196.989  -63.420    8.153   56.208  286.389 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  2630.07     467.90   5.621 0.000221 ***
    ## X1            -48.48      45.17  -1.073 0.308343    
    ## X2            151.20      46.52   3.250 0.008714 ** 
    ## X3            705.20     204.04   3.456 0.006162 ** 
    ## X7            -14.08      12.14  -1.159 0.273339    
    ## X8             44.32      26.51   1.672 0.125549    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 146.5 on 10 degrees of freedom
    ## Multiple R-squared:  0.9382, Adjusted R-squared:  0.9074 
    ## F-statistic: 30.38 on 5 and 10 DF,  p-value: 9.74e-06

krok 5, nowy model regresji liniowej, bez zmiennej objaśniającej X4, X6,
X5 i X1: stopa bezrobocia \[%\]

    model_5 <- lm(Y ~ X2 + X3 + X7 + X8, data = dane)
    summary(model_5)

    ## 
    ## Call:
    ## lm(formula = Y ~ X2 + X3 + X7 + X8, data = dane)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -230.201  -79.194    3.082   65.403  270.955 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  2390.68     414.15   5.772 0.000124 ***
    ## X2            135.04      44.32   3.047 0.011103 *  
    ## X3            764.39     197.80   3.864 0.002633 ** 
    ## X7            -12.96      12.18  -1.064 0.310222    
    ## X8             42.78      26.65   1.605 0.136780    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 147.5 on 11 degrees of freedom
    ## Multiple R-squared:  0.9311, Adjusted R-squared:  0.9061 
    ## F-statistic: 37.18 on 4 and 11 DF,  p-value: 2.491e-06

krok 6, nowy model regresji liniowej, bez zmiennej objaśniającej X4, X6,
X5, X1 i X7: liczba nowo utworzonych miejsc pracy \[tys.\]

    model_6 <- lm(Y ~ X2 + X3 + X8, data = dane)
    summary(model_6)

    ## 
    ## Call:
    ## lm(formula = Y ~ X2 + X3 + X8, data = dane)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -240.04  -62.76   22.30   84.86  218.12 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 2365.118    415.719   5.689 0.000101 ***
    ## X2           121.912     42.796   2.849 0.014658 *  
    ## X3           809.122    194.337   4.164 0.001315 ** 
    ## X8            14.700      3.704   3.969 0.001863 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 148.4 on 12 degrees of freedom
    ## Multiple R-squared:  0.924,  Adjusted R-squared:  0.905 
    ## F-statistic: 48.66 on 3 and 12 DF,  p-value: 5.449e-07

Wszystkie zmienne są istotne statystycznie. Nowy model zawiera 3 zmienne
objaśniające: X2, X3 i X8.

## Identyfikacja obserwacji nietypowych

Przed sprawdzeniem założeń klasycznej metody najmniejszych kwadratów
należy upewnić się, że nie występują obserwacje nietypowe, które
zniekształcają wyniki. Badany model zawiera niewielką grupę obserwacji -
16 wojewódstw.

    # k - ilość zmiennych objaśniających + 1 wyraz wolny
    k <- length(coef(model_6))

    # n - ilosc obserwacji
    n <- nrow(dane)

1.  Obserwacje wpływowe - Zniekształcają model. Usunięcie takiej
    obserwacji może zmienić oszacowania parametrów strukturalnych
    modelu.

<!-- -->

    dffits <- dffits(model_6)
    wplywowe <- which(abs(dffits) > 2*sqrt(k/n))
    plot(dffits,
         main = "Wykres obserwacij wpływowych",
         xlab="",
         ylab="DFFITS",
         col = "blue",
         pch = 19);abline(h=0, col="purple")

![Image](https://github.com/mariamiecho/Analiza-wynagrodzen-w-Polsce-R/blob/main/Wykres_obserwacji_wplywowych.jpg)

W badanym modelu obserwacjami wpływowymi są obserwacje 7 i 12 czyli
wojewódstwo mazowieckie i śląskie.

2.  Obserwacje odstające - są to nietypowe wartości zmiennej objaśnianej
    (Y). Charakteryzują się dużymi wartościami reszt.

<!-- -->

    rstud <- rstandard(model_6)
    print(rstud)

    ##           1           2           3           4           5           6 
    ##  1.61787767 -1.50513478 -0.28697889  0.60411331 -1.73927605  0.05940241 
    ##           7           8           9          10          11          12 
    ##  0.93673180  0.42854026 -0.46600409  1.26198470 -0.56831017  0.92134436 
    ##          13          14          15          16 
    ##  0.69190356  0.28736887 -1.50382501 -0.11500729

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

![](RMarkDown_projekt_files/figure-markdown_strict/unnamed-chunk-12-1.png)
![Image](https://github.com/mariamiecho/Analiza-wynagrodzen-w-Polsce-R/blob/main/Wykres_obserwacji_odstajacych.jpg)

W badanym modelu nie występują obserwacje odstające.

3.  Obserwacje dźwigniowe - są to nietypowe wartości zmiennej
    objaśniającej (X).

<!-- -->

    hatv <- hatvalues(model_6)
    dzwigniowe <- which(hatv > 0.5)
    barplot(hatv,
            col="lightblue",
            main = "Wykres obserwacji dźwigniowych",
            xlab = "Wojewódstwa",
            ylab = "hi"
    );abline(h = 0.5, col="purple")

![](RMarkDown_projekt_files/figure-markdown_strict/unnamed-chunk-13-1.png)
![Image](https://github.com/mariamiecho/Analiza-wynagrodzen-w-Polsce-R/blob/main/Wykres_obserwacji_dzwigniowych.jpg)
W badanym modelu obserwacjami dźwigniowymi są obserwacje 7 i 12 czyli
wojewódstwo mazowieckie i śląskie.

## Współliniowość

Występuje gdy zmienne objaśniające są silnie skorelowane między sobą.
Statystyka testwa: VIF = 1/(1-R^2 ). Jeżeli VIF &gt; 10 oznacza to, że
występuje współliniowość, która obniża jakość modelu.

    vif(model_6)

    ##       X2       X3       X8 
    ## 1.684368 1.477454 2.280300

Wszystkie zmienne objaśniające mają VIF &lt; 10. Brak współliniowości.

## Istotność parametrów

Należy sprawdzić czy zmienne objaśniające są istotne statystycznie.

    # H0: βj = 0    (parametr strukturalny nie jest statystycznie istotny)
    # H1: βj =/= 0 (parametr strukturalny jest statystycznie istotny)
    summary(model_6)

    ## 
    ## Call:
    ## lm(formula = Y ~ X2 + X3 + X8, data = dane)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -240.04  -62.76   22.30   84.86  218.12 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 2365.118    415.719   5.689 0.000101 ***
    ## X2           121.912     42.796   2.849 0.014658 *  
    ## X3           809.122    194.337   4.164 0.001315 ** 
    ## X8            14.700      3.704   3.969 0.001863 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 148.4 on 12 degrees of freedom
    ## Multiple R-squared:  0.924,  Adjusted R-squared:  0.905 
    ## F-statistic: 48.66 on 3 and 12 DF,  p-value: 5.449e-07

Dla każdej zmiennej p-value &lt; 0.05 co oznacza, że odrzucamy H0 na
rzecz H1. Zmienne są istotne statystycznie.

## Badanie normalności - test shapiro-wilka

    # H0:   Składnik losowy modelu ma rozkład normalny
    # H1:   Składnik losowy modelu nie ma rozkładu normalnego
    reszty <- resid(model_6)
    shapiro.test(reszty)

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  reszty
    ## W = 0.94519, p-value = 0.4175

P-value &gt; 0.05 co oznacza, że jest brak podstaw do odrzucenia H0.
Badany model ma rozkład normalny.

    qqnorm(reszty,
           main = "Wykres rozkładu normalnego",
           col="blue",
           pch = 19);qqline(reszty)

![](RMarkDown_projekt_files/figure-markdown_strict/unnamed-chunk-17-1.png)
![Image](https://github.com/mariamiecho/Analiza-wynagrodzen-w-Polsce-R/blob/main/Wykres_rozkl_normalnego.jpg)

## Badanie autokorelacji - test Durbina-Watsona

W modelu regresji liniowej autokorelacja reszt oznacza, że reszty są
same ze sobą skorelowane.

    # H0: współczynnik autokorelacji ρ = 0 (występuje brak autokorelacji) 
    # H1: współczynnik autokorelacji ρ > 0 (występuje autokorelacja dodatnia) 
    durbinWatsonTest(model_6)

    ##  lag Autocorrelation D-W Statistic p-value
    ##    1      -0.3021371      2.423148   0.372
    ##  Alternative hypothesis: rho != 0

P-value &gt; 0.05 oznacza brak podstaw do odrzucenia H0. Nie występuje
autokorelacja reszt, nie są one od siebie zależne.

## Badanie homoskedastyczności - test Goldfelda-Quandta

Homoskedastyczność oznacza to, że wariancja reszt jest taka sama dla
wszystkich wartości zmiennych niezależnych.

    # H0:   σ1^2=σ2^2   składnik losowy modelu jest homoskedastyczny    
    # H1:   σ1^2 != σ2^2    składnik losowy modelu jest heteroskedastyczny  
    gqtest(model_6)

    ## 
    ##  Goldfeld-Quandt test
    ## 
    ## data:  model_6
    ## GQ = 0.35098, df1 = 4, df2 = 4, p-value = 0.8326
    ## alternative hypothesis: variance increases from segment 1 to 2

P-value &gt; 0.05 oznacza to brak podstaw do odrzucenia H0. Składnik
losowy jest homoskedastyczny.

## Dopasowanie modelu do danych empirycznych

    summary(model_6)

    ## 
    ## Call:
    ## lm(formula = Y ~ X2 + X3 + X8, data = dane)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -240.04  -62.76   22.30   84.86  218.12 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 2365.118    415.719   5.689 0.000101 ***
    ## X2           121.912     42.796   2.849 0.014658 *  
    ## X3           809.122    194.337   4.164 0.001315 ** 
    ## X8            14.700      3.704   3.969 0.001863 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 148.4 on 12 degrees of freedom
    ## Multiple R-squared:  0.924,  Adjusted R-squared:  0.905 
    ## F-statistic: 48.66 on 3 and 12 DF,  p-value: 5.449e-07

Postać oszacowana modelu ekonometrycznego:

Ŷ= 2365,12 +121,91X2+809,12X3+ 14,7X8

Interpretacja:

-   X2: Jeśli średnia cena lokali mieszkalnych za m2 (x2) wzrośnie w
    danym województwie o tysiąc złotych, to przeciętne miesięczne
    wynagrodzenie (y) w tym wojewódzwie wzrośnie średnio o 121.91zł przy
    założeniu, że miesięczne wydatki na jedną osobę i liczba
    zlikwidowanych miejsc pracy się nie zmienią.

-   X3: Jeżeli średnie miesięczne wydatki na jedną osobę (x3) w danym
    wojewódzwie wzrosną o tysiąc złotych to przeciętne miesięczne
    wynagrodzenie (y) w tym wojewódzwie wzrośnie średnio o 809.12zł przy
    założeniu, że średnia cena lokali mieszkalnych za m2 i liczba
    zlikwidowanych miejsc pracy się nie zmienią.

-   X8: Jeżeli liczba zlikwidowanych miejsc pracy (x8) w danym
    województwie wzrośnie o tysiąc to przeciętne miesięczne
    wynagordzenie (y) w tym wojewódzwie wzrośnie średnio o 14,70zł przy
    założeniu, że średnia cena lokali mieszkalnych za m2 oraz miesięczne
    wydatki na jedną osobę nie zmienią się.

-   Współczynnik determinacji R^2: 92,4% całkowitej zmienności
    miesięcznego wynagrodzenia zostało wyjaśnione modelem.

-   Współczynnik korelacji R: korelacja między miesięcznym
    wynagrodzeniem a ceną lokali mieszkalnych za m2, miesięcznymi
    wydatkami a zlikwidowanymi miejscami pracy jest bardzo silna.

-   Odchylenie standardowe Se: Wartość empiryczna różni się średnio o
    148,35zł na osobę od wartości teoretycznych.

## Wnioskowanie

Na podstawie przedstawionych informacji oraz przeprowadzonej analizy
można stwierdzić, że przeciętne wynagrodzenie w regionie zależy od
różnych czynników ekonomicznych, demograficznych i rynkowych. Jednym z
nich jest średnia cena mieszkań, która odzwierciedla poziom rozwoju
gospodarczego oraz atrakcyjność regionu. Wyższe ceny mieszkań często
wiążą się z wyższymi kosztami życia, co wywołuje presję na wzrost płac.
Dodatkowo, takie regiony zazwyczaj charakteryzują się obecnością lepiej
wynagradzanych sektorów gospodarki.

Istotnym czynnikiem są także miesięczne wydatki na osobę, które obrazują
koszty życia. W zamożniejszych regionach, gdzie wydatki są wyższe,
wynagrodzenia są zwykle dostosowywane, aby zapewnić mieszkańcom
odpowiedni standard życia.

Liczba zlikwidowanych miejsc pracy również wpływa na wynagrodzenia. Gdy
redukcje dotyczą sektorów o niskich płacach, przeciętne wynagrodzenie
może wzrosnąć, ponieważ dominują lepiej opłacane stanowiska.
