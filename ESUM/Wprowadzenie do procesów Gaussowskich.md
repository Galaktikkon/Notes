
### Własności macierzy kowariancji

```ad-przypomnienie
Macierzą kowariancji wielowymiarowej zmiennej losowej $\text{x}$ o wartości oczekiwanej $\mu$ jest macierz:
$$
\Sigma:= \mathbb{E}\left[(\text{x}-\mu)(\text{x}-\mu)^{\text{T}}\right]
$$
```

Taka macierz ma na przekątnej wariancję $\text{x}$, a poza nią kowariancję wymiarów tego wektora.

```ad-wlasnosc
Każda macierz kowariancji jest nieujemnie określona, tj. dla każdego wektora $\text{v}$:
$$
\text{v}^{\text{T}}\Sigma\text{v} \geq 0
$$
```

```ad-dowod
Zwróćmy uwagę, że $\text{v}^{\text{T}}(\text{x}-\mu)$ to skalar. Oznaczmy go przez $z$.

Wtedy: 
$$
\text{v}^{\text{T}}\Sigma\text{v} = \text{v}^{\text{T}}\mathbb{E}\left[(\text{x}-\mu)(\text{x}-\mu)^{\text{T}}\right]\text{v}
$$
Z liniowości wartości oczekiwanej
$$
= \mathbb{E}\left[\text{v}^{\text{T}}(\text{x}-\mu)(\text{x}-\mu)^{\text{T}}\text{v}\right]
$$
Korzystamy z naszego oznaczenia, ponadto transpozycja skalaru daje skalar, więc:
$$
= \mathbb{E}\left[zz^{\text{T}}\right] = \mathbb{E}\left[z^2\right] \geq 0
$$
```

```ad-wlasnosc
Każda (symetryczna) macierz nieujemnie określona jest macierzą kowariancji (istnieje dla niej pewna zmienna losowa).
```

```ad-dowod

Niech $\text{A}$ będzie macierzą nieujemnie określoną. Ponieważ jest ona nieujemnie określona to istnieje macierz $\text{L}$ taka, że:
$$
\text{A} = \text{L}\text{L}^{\text{T}}
$$

Powyższa dekompozycja macierzy $\text{A}$ nazywa się ***[ dekompozycją Choleskiego macierzy](https://pl.wikipedia.org/wiki/Rozk%C5%82ad_Choleskiego)***.

Weźmy zmienną losową:
$$
\text{z} \sim N(\textbf{0}, \textbf{I})
$$

Następnie policzmy macierz kowariancji zmiennej $\textbf{Lz}$
$$
\Sigma_{\textbf{Lz}} = \mathbb{E}\left[(\textbf{Lz}-\mu_{\textbf{Lz}})(\textbf{Lz}-\mu_{\textbf{Lz}})^{\text{T}}\right]
$$

Z liniowości wartości oczekiwanej $\mu_{\textbf{Lz}} = \mu_{\textbf{L}} \cdot \mu_{\textbf{z}} = L \cdot 0$:

$$
= \mathbb{E}\left[(\textbf{Lz}-\textbf{L0})(\textbf{Lz}-\textbf{L0})^{\text{T}}\right]
$$

Z liniowości wartości oczekiwanej (znowu):

$$
= \mathbb{E}\left[\textbf{Lz}\textbf{z}^{\text{T}}\textbf{L}^{\text{T}}\right] =
\textbf{L}\mathbb{E}\left[\textbf{z}\textbf{z}^{\text{T}}\right]\textbf{L}^{\text{T}}
$$
Wartość oczekiwana $\textbf{z}\textbf{z}^{\text{T}}$ to kowariancja zmiennej losowej $z$, czyli $\textbf{I}$:
$$
= \textbf{LI}\textbf{L}^{\textbf{T}} = \textbf{A}
$$

*Wniosek:*

Wzięliśmy dowolną nieujemnie określoną macierz $\textbf{A}$ i przy pomocy dekompozycji Choleskiego wyznaczyliśmy zmienną losową dla której $\textbf{A}$ jest macierzą kowariancji - **c.n.d**.
```

Powyższe własności wskazują, że macierze kowariancji można w pewnym sensie utożsamiać z macierzami nieujemnie określonymi.

### Funkcje kowariancji

Wykorzystajmy powyższe własności do tworzenia macierzy kowariancji. 

Wyobraźmy sobie, że wytrzasnęliśmy funkcję $k: \mathbb{R}^d \times \mathbb{R}^d \rightarrow \mathbb{R}$, czyli funkcję, która przyjmuje dwa wektory $d$-wymiarowe i zwraca skalar.
- najprostszą taką funkcja może być po prostu iloczyn skalarny
Ponadto funkcja ta musi spełniać warunek: $\forall n \in \mathbb{N}$, $\forall \text{x}_1,\text{x}_2,...,\text{x}_n \in \mathbb{R}$ macierz:

$$
\textbf{K}:= 
\left[
	\begin{matrix}
		k(\text{x}_1, \text{x}_1) & k(\text{x}_1, \text{x}_2) & \cdots & k(\text{x}_1, \text{x}_n) \\
		k(\text{x}_2, \text{x}_1) & k(\text{x}_2, \text{x}_2) & \cdots & k(\text{x}_2, \text{x}_n) \\
		\vdots & \vdots & \ddots & \vdots \\ 
		k(\text{x}_n, \text{x}_1) & k(\text{x}_n, \text{x}_2) & \cdots & k(\text{x}_n, \text{x}_n)
	\end{matrix}
\right]
$$

jest dodatnio określona. Wówczas $k$ nazywamy ***jądrem dodatnio określonym*** (ang. *positive definite kernel*) lub alternatywnie ***jądrem Mercera*** (ang. *Mercer kernel*). Jądra dodatnio określone można traktować jako swoiste "przepisy" na macierz kowariancji.
- stąd inna powszechna nazwa - ***funkcje kowariancji***

Dobra, ale skąd takie coś otrzymać? Ogólnie analiza funkcyjna dorobiła się narzędzi  skąd tę funkcję wziąć i jakie [warunki musi spełniać](https://en.wikipedia.org/wiki/Mercer%27s_theorem), ale dla nas nie będzie priorytetem dowodzenie czy też szukanie takich funkcji. Skorzystamy z gotowych podręcznikowych tabel funkcji, do wprowadzenia procesów Gaussowskich.

```ad-przyklady
- Jądro Gaussowskie:
	$$
	k(\text{x}_1, \text{x}_2) = \exp\left[-\frac{||\text{x}_1-\text{x}_2||^2}{2l^2}\right]
    $$
    gdzie $l$ jest dowolną niezerową stałą.
- Jądro periodyczne
	$$
	k(\text{x}_1, \text{x}_2) = \exp\left[-\frac{2}{l^2}\sin^2\left(\pi\frac{||\text{x}_1-\text{x}_2||}{p}\right)\right]
    $$
    gdzie $l$ oraz $p$ są dowolnymi niezerowymi stałymi.
```

```ad-wlasnosc
Złożenie funkcji kowariancji z wielomianem o nieujemnych współczynnikach:
$$
k' = q \circ k
$$
gdzie $k$ to funkcja kowiariancji zaś $q$ to wielomian o nieujemnych współczynnikach

Suma lub iloczyn dwóch funkcji kowariancji również jest funkcją kowariancji.
```

Wprowadźmy uproszczoną notację dla wartości funkcji kowariancji - będzie nam wygodniej się nimi posługiwać.

Dla dwóch zbiorów wektorów:

$$
X = \{\text{x}_1, \text{x}_2,...,\text{x}_n\} \subset \mathbb{R}^d
$$
$$
Y = \{\text{y}_1, \text{y}_2,...,\text{y}_m\} \subset \mathbb{R}^d
$$

będziemy pisać:

$$
\textbf{K}_{XY} = k(X,Y) =  
\left[
	\begin{matrix}
		k(\text{x}_1, \text{y}_1) & k(\text{x}_1, \text{y}_2) & \cdots & k(\text{x}_1, \text{y}_m) \\
		k(\text{x}_2, \text{y}_1) & k(\text{x}_2, \text{y}_2) & \cdots & k(\text{x}_2, \text{y}_m) \\
		\vdots & \vdots & \ddots & \vdots \\ 
		k(\text{x}_n, \text{y}_1) & k(\text{x}_n, \text{y}_2) & \cdots & k(\text{x}_n, \text{y}_m)
	\end{matrix}
\right]
$$

W efekcie powstaje nam macierz $n \times m$, aplikując na każdą parę wektorów z różnych zbiorów $X$ i $Y$
funkcję kowariancji.
- nie jest to macierz kowariancji, w szczególności nie jest ona macierzą kwadratową!

### Proces Gaussowski

Niech

$$
GP = \{f_x; x \in \mathbb{R}^d\}
$$

będzie rodziną (zbiorem) zmiennych losowych indeksowanych przez punkty z $\mathbb{R}^d$.

```ad-przyklady
Wyobraźmy sobie pomieszczenie, w którym próbujemy zbadać temperaturę. Niestety nie mamy dokładnej, perfekcyjnej wiedzy o temperaturze w każdym punkcie, ale mamy dla nich rozkłady prawdopodobieństwa nad ich temperaturami:

![[Pasted image 20240613231813.png]]

```

Mówimy, że $GP$ jest ***procesem Gaussowskim*** wtedy i tylko wtedy jeśli każdy jego skończony podzbiór ma łącznie (wielowymiarowy) rozkład normalny. ^e772f7
- w szczególności każdy punkt z osobna musi mieć rozkład normalny (jako, że to jednoelementowy podzbiór)

Ubierzmy to w jakąś zgrabną majcę. Mamy przestrzeń $d$-wymiarową i wybieramy dowolne $n$ punktów - dowolną skończoną liczbę.

$$
\forall n \in \mathbb{N}, X = \{\text{x}_1, \text{x}_2,...,\text{x}_n\} \subset \mathbb{R}^d
$$

Bierzemy zmienne losowe, które łącznie **muszą** mieć rozkład normalny.

$$
\begin{equation}
\left[ \begin{matrix} f_{\textbf{x}_1} \\ f_{\textbf{x}_2} \\ \vdots \\ f_{\textbf{x}_n}
\end{matrix} \right] \sim N(\textbf{$\mu_X$}, \textbf{$\Sigma_X$})
\end{equation}
$$

Jeżeli dla każdej skończonej kombinacji punktów otrzymamy rozkład normalny, to mówmy, że te punkty łącznie tworzą proces Gaussowski.

Wypadałoby teraz pokazać jak taki abstrakcyjny koncept skonstruować - jak zbudować proces Gaussowski. Zauważmy, że wartość oczekiwana i macierz kowariancji zależą od podzbioru, który wybieramy z procesu. W zasadzie mamy już pewien [[#Funkcje kowariancji|"przepis"]] pozwalający przerobić zbiór punktów na macierz kowariancji. Oczywiście jest to funkcja kowariancji.

$$
\Sigma_X = k(X,X) = \textbf{K}_{XX}
$$

 - nie oznacza to, że jesteśmy w stanie teraz skonstruować dowolny proces Gaussowski jaki nam przyniesie fanaberia, ale jeżeli będzie nam dane zrobić "jakiś" proces Gaussowski w przestrzeni euklidesowej, to jak najbardziej mamy do tego narzędzia
Problem stanowi wartość oczekiwana. My jednak nie będziemy się tym zadręczać i przyjmiemy $\mu_X = 0$ - w praktyce tak często się postępuje.

Zatem konstruując proces Gaussowski dla pewnego zbioru punktów, mówimy, że te punkty łącznie są opisane rozkładem normalnym, w którym wartość oczekiwana wynosi 0 (dla każdego z osobna także), a kowariancja tych punktów będzie określona przy pomocy funkcji kowariancji $k$. Daje nam to w praktyce rozkład prawdopodobieństwa nad funkcjami:

$$
f \sim GP(\textbf{0},k)
$$
$$
f: \mathbb{R}^d \rightarrow \mathbb{R}
$$

```ad-przyklady
Wyobraźmy sobie, że już udało nam się skonstruować proces Gaussowski przy pomocy jądra kowariancji. Czym on tak naprawdę jest? Możemy go potrakować jako rozkład prawdopodobieństwa nad funkcjami z $\mathbb{R}^d$ w $\mathbb{R}$. Ale o co tu biega?

Wybieramy dowolny zbiór punktów $X$ które łącznie mają rozkład normalny $N(0, K_{XX})$. Wyciągamy próbkę z tego rozkładu $f \sim N(0, K_{XX})$. Będzie to po prostu wektor liczb, gdzie każda z nich jest przypisana do konkretnego punktu. Możemy sobie wyobrazić, że mamy funkcję $f$ w wielowymiarowej przestrzeni i właśnie policzyliśmy jej wartości w pewnych dowolnie wybranych punktach. Innymi słowy, bierzemy funkcję opisaną jądrem kowariancji, przykładamy ją do wybranego zbioru punktów i dostajamy jej wartości w tych punktach.

![[Pasted image 20240614021907.png]]

```

Oczywiście ten rozkład nie "przedstawia" funkcji w postaci analitycznej!
- to nie działa w taki sposób, że próbkujemy jakiś konkretny wzór typu $x^2-1$

Jednak dla każdego skończonego zbioru punktów $X = \{\text{x}_1, \text{x}_2,...,\text{x}_n\} \subset \mathbb{R}^d$ proces Gaussowski daje nam rozkład prawdopodobieństwa nad wartościami $f$ w punktach z $X$:

$$
\textbf{f}_X = 
\begin{equation}
\left[ \begin{matrix} f(\textbf{x}_1) \\ f(\textbf{x}_2) \\ \vdots \\ f(\textbf{x}_n)
\end{matrix} \right] \sim N(0, \textbf{K}_{XX})
\end{equation}
$$
Otrzymaliśmy mechanizm konstruowania funkcji z przestrzenie euklidesowej $\mathbb{R}^d$ w $\mathbb{R}$ w sposób probabilistyczny - możemy opisać prior nad funkcjami.

Jaką rolę pełni funkcja kowariancji w tym przedsięwzięciu? 
 - Funkcja kowariancji w pewnym sensie wyraża zależności pomiędzy wartościami $f$ w różnych punktach $\mathbb{R}^d$.
 - Postulując funkcję kowariancji określamy więc jakich cech oczekujemy od $f$:
	 - Periodyczność, gładkość, etc.
 - W efekcie uzyskujemy ***ekspresywny prior nad funkcjami***.

```ad-przyklady
Weźmy procesy Gaussowskie dla $d=1$ (czyli $\mathbb{R} \rightarrow \mathbb{R}$) 

![[Pasted image 20240614024021.png]]

Mamy tutaj 3 próbki. Wzięto pewną liczbę punktów na osi x, wyliczono rozkład normalny wynikający z tego procesu i wyciągnięto próbkę - wartości $f$ w tych punktach. Rzeczywiście wygląda jakbyśmy wyciągali funkcje z rozkładu.

![[Pasted image 20240614024349.png]]

Tutaj też 3 próbki, ale z innym hiper-parametrem.
```

### Procesy Gaussowskie w regresji

Po całym teoretycznym kotle dochodzimy do celu zastosowania procesów Gaussowskich. Możemy je wykorzystać jako prior w Bayesowskim modelowaniu funkcji.
- np. w zagadnieniu regresji - mówimy wówczas o ***regresji procesem Gaussowskim*** (ang. *Gaussian process regression*. Nie będziemy przypisywać prostych czy też hiperpłaszczyzn, ale pewne funkcje.

Załóżmy, że obserwujemy wartości pewnej nieznanej funkcji $f$ w punktach:

$$
U = \{\textbf{u}_1, \textbf{u}_2,..., \textbf{u}_n\} \subset \mathbb{R}^d 
$$

Tak się składa, że znamy wartości jakiejś funkcji $f$ w tych punktach - ktoś nam zrobił przysługę i pomierzył. Pech chciał, że (jak to na tym kursie) nie znamy dokładnej wartości w tych punktach, obserwowane wartości są obarczone pewnym błędem:

$$
y_i = f(\textbf{u}_i) + \epsilon, \text{ } \epsilon \sim N(0, \sigma^2)
$$

^baa98e

Chcemy oszacować wartości funkcji w innych punktach, których nie mierzyliśmy. Interesuje nas rozkład prawdopodobieństwa nad wartościami $f$ w punktach:

$$
U = \{\textbf{v}_1, \textbf{v}_2,..., \textbf{v}_m\} \subset \mathbb{R}^d 
$$

```ad-przyklady

Mamy zbiór punktów, które leżą jakiejś potencjalnej funkcji, której wartości znamy z pewną niepewnością. Zadajemy sobie pytanie, jaką wartość przyjmie funkcja w nieznanym punkcie - czego się możemy spodziewać.
 - np. znamy temperaturę (obarczoną błędem) tylko w kilku punktach w pomieszczeniu (umieślilismy tam termometry), ale chcemy poznać temperaturę w miejscu gdzie nie ma termometru.

![[Pasted image 20240614031955.png]]
```

Zdefiniujmy parametry naszego modelu.  Funkcją $f$, którą szacujemy ma a priori rozkład opisany procesem Gaussowskim o zerowej wartości oczekiwanej i "przepisem" na macierz kowariancji danym jako funkcję $k$:

$$
f \sim GP(\textbf{0},k)
$$

Naszą wiarygodnością będą wartości $\textbf{y}$ danych przez funkcję $f$ w punktach $U$, które mają rozkład normalny o wartości oczekiwanej równiej wartościom funkcji $f$ w punktach $U$ obarczonymi [[#^baa98e|błędem]] $\sigma^2$:

$$
\textbf{y | }f, U \sim N(\textbf{f}_U, \sigma^2\textbf{I})
$$

Zakładamy, że każdy punkt ma błąd niezależny od innych punktów, więc możemy rozdzielić to na sumę:

$$
\text{cov}\left[\textbf{y | }f, U \right] = \text{cov}\left[\textbf{f}_U\right] + \text{cov}\left[\epsilon\right] = \textbf{K}_{UU} + \sigma^2\textbf{I}
$$

Dalej, zgodnie z [[#^e772f7|definicją procesu Gaussowskiego]] wartości $f$ nad dowolnym skończonym zbiorem punktów mają rozkład normalny. Skoro tak, to $\textbf{y}$ oraz $\textbf{f}_V$, punkty , które znamy oraz w te, które szacujemy mają łącznie rozkład normalny:

$$
\left[ \begin{matrix} \textbf{y} \\ \textbf{f}_V
\end{matrix} \right] \sim N(0, \left[ \begin{matrix} \textbf{K}_{UU} + \sigma^2\textbf{I} & \textbf{K}_{UV} \\ \textbf{K}_{VU} & \textbf{K}_{VV}
\end{matrix} \right] )
$$

[[Modele Gaussowskie#^e2acc7|Wiemy]], że skoro dwie zmienne łącznie mają rozkład normalny, to osobno też i tak też będzie z rozkładami warunkowymi. Pozostało nam wrzucić nasze dane do wzorów z [[Modele Gaussowskie#Liniowe Modele Gaussowskie|liniowego modelu Gaussowskiego]].

$$
\textbf{f}_V \textbf{ | y, }U \sim N(\mu_V, \Sigma_V)  
$$

Teraz trochę magii algebry i podstawiania do wzorów (elementarna sprawa):

$$
\mu_V = \textbf{K}_{VU}\left(\textbf{K}_{UU} + \sigma^2\textbf{I}\right)^{-1}\textbf{y}
$$
$$
\Sigma_V = \textbf{K}_{VV} - \textbf{K}_{VU}\left(\textbf{K}_{UU} + \sigma^2\textbf{I}\right)^{-1}\textbf{K}_{UV}
$$

Rozkład warunkowy $p(\textbf{f}_V \textbf{ | y, }U)$ jest w tym modelu rozkładem ***predykcyjnym***:
- Pozwala nam wnioskować o wartościach funkcji $f$ w punktach ze zbioru $V$ gdy znamy przybliżane wartości w punktach ze zbioru $U$.
- Funkcja kowariancji wyraża natomiast nasze założenia a priori co do charakteru funkcji $f$.