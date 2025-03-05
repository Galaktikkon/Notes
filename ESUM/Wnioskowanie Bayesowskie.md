
#### Twierdzenie Bayesa

Jeśli zdarzenia $A_i$ spełniają założenia twierdzenia o prawdopodobieństwie
całkowitym oraz dodatkowo $P(B) > 0$, to dla każdego $k$ zachodzi równość:

$$
\begin{equation}
P(A_k | B) = \frac{P(B | A_k)P(A_k)}{P(B)} = \frac{P(B | A_K)P(A_k)}{\sum_i P(B|A_i)P(A_i)}
\end{equation}
$$

Ten wzór pozwala nam uaktualnić naszą wiedzę na temat zdarzenia, pod warunkiem, że posiadamy pewne dodatkowe informacje (jest to po prostu wzór na prawdopodobieństwo warunkowe).

#### Wnioskowanie

Weźmy $D$ - obserwacje, $H_1, H_2, ..., H_k$ - hipotezy.

$$
\begin{equation}
P(H_k | D) = \frac{P(D | H_k)P(H_k)}{P(D)} = \frac{P(D | H_K)P(H_k)}{\sum_i P(D|H_i)P(H_i)}
\end{equation}
$$

O czym świadczy zaobserwowanie zdarzenia $D$?
 - Zdarzenie $D$ jest **faktem**, zaobserwowanym pewnikiem.
	 - np. otrzymanie wiadomości e-mail, pomiar temperatury

Zakładamy, że istnieją pewne *hipotezy* $H_i$ dotyczące zdarzenia $D$ i zastanawiamy się, które z nich stoją za tym zdarzeniem. 

Jeśli jesteśmy w stanie określić jakie jest prawdopodobieństwo, że naszą **obserwację** $D$ wygenerowała pewna hipoteza,
- np. jakie jest prawdopodobieństwo, że wiadomość e-mail została napisana przez bota ($P(D | H_i)$)
to obliczeniu prawej strony równania, jesteśmy w stanie określić interesujące nas prawdopodobieństwo (szansa, że bot napisał wiadomość po zaobserwowaniu tej wiadomości), czyli $P(H_i|D)$

Takie spojrzenie na wzór Bayesa pozwala aktualizować naszą wiedzę o hipotezach na podstawie obserwacji.

Dodajmy parę oznaczeń porządkowych:

![[bayes.png | center|700]]

  - *prior* - prawdopodobieństwo przed zaobserwowaniem jakiegokolwiek zdarzenia
	  - np. nasza niezawodna intuicja mówi nam, że 5% przychodzących wiadomości mailowych to spam (przed przeczytaniem maila)
 - *likelihood* - prawdopodobieństwo zaobserwowania danych pod warunkiem hipotezy
	 - np. jaka jest szansa na zaistnienie pewnej wiadomości, jeżeli wygenerował ją bot (lub człowiek)
- *posterior* - prawdopodobieństwo hipotezy pod warunkiem zaobserwowania pewnych danych ^3eafd3
	- np. prawdopodobieństwo wygenerowania otrzymanej wiadomości przez bota po jej przeczytaniu (po zapoznaniu się z jej treścią - *obserwacja*)
 - *marginal likelihood* (przesłanka za modelem) - prawdopodobieństwo zaobserwowania danych pod warunkiem modelu (marginalizacja po zmiennej losowej)
	 - opis jak nasz model pasuje do świata

#### Przykład: Czy gra jest uczciwa?

Załóżmy, że jesteśmy szulerem i zastanawiamy się czy do stołu, przy którym siedzimy, zasiedli oszuści. Zastanawiamy się czy już powinniśmy się zwijać. 

Spróbujmy ubrać to w model statystyczny. Naszymi założeniami będą:
 - Jeśli gra jest uczciwa, to szansa wygranej wynosi 0.5 (*likelihood*)
 - Jeśli gra jest nieuczciwa, to przeciwnik zawsze wygrywa (*likelihood*) ^b5c3b1
 - W naszej ocenie prawdopodobieństwo *a priori*, że gra jest nieuczciwa 0.1

Wtedy:

$$
\begin{equation}
	P(\text{przegrana } | \text{ uczciwa}) = 0.5
\end{equation}
$$
$$
\begin{equation}
	P(\text{przegrana } | \text{ nieuczciwa}) = 1
\end{equation}
$$

oraz:

$$
\begin{equation}
	P(n \text{ przegranych } | \text{ uczciwa}) = \left( \frac{1}{2} \right) ^n
\end{equation}
$$

^de37ac
Stwierdzamy, że akceptujemy ryzyko i gramy. Cosik udaje nam się wygrać, ale nagle przegrywamy kilka razy z rzędu, a natrętne myśli o uczciwości gry nie dają nam spokoju. Głowimy się nad tym co tak naprawdę dzieje się przy tym stoliku. Zaczynamy się zastanawiać nad prawdopodobieństwem:

$$
\begin{equation}
	P(\text{nieuczciwa } \text{| } n\text{ przegranych}) = \text{ ???}
\end{equation}
$$


które jest oczywiście prawdopodobieństwem *a posteriori*.


Zapiszmy to korzystając ze wzoru Bayesa:

$$
\begin{equation}
	??? = \frac{P(n \text{ przegranych } | \text{ nieuczciwa})P(\text{nieuczciwa})}{P(n \text{ przegranych } | \text{ nieuczciwa})P(\text{nieuczciwa})+P(n \text{ przegranych } | \text{ uczciwa})P(\text{uczciwa})}
\end{equation}
$$

Z [[#^b5c3b1| drugiego założenia]] wynika, że:

$$
\begin{equation}
	= \frac{1\cdot P(\text{nieuczciwa})}{1 \cdot P(\text{nieuczciwa})+P(n \text{ przegranych } | \text{ uczciwa})P(\text{uczciwa})}
\end{equation}
$$

Idąc dalej, z [[#^de37ac|wniosku]]:

$$
\begin{equation}
	= \frac{P(\text{nieuczciwa})}{P(\text{nieuczciwa})+\left(\frac{1}{2}\right)^n \cdot P(\text{uczciwa})}
\end{equation}
$$

Ponadto, $P(\text{uczciwa}) = 1- P(\text{nieuczciwa})$, zatem:

$$
\begin{equation}
	= \frac{P(\text{nieuczciwa})}{P(\text{nieuczciwa})+\left(\frac{1}{2}\right)^n \cdot (1 - P(\text{nieuczciwa}))}
\end{equation}
$$

Nasze urojenia dotyczące znaczonych kart możemy przedstawić na wykresie:

![[gra_wykres.png]]

Wzór Bayesa pozwala z każdą kolejną grą (obserwacją) aktualizować naszą ocenę rzeczywistości.
Nie jest sposób na ocenę realizacji zdarzeń pod warunkiem znajomości dużej liczby prób (nie jest to [[Interpretacje prawdopodobieństwa#^4e701b|wnioskowanie częstotliwościowe]]), jest to sposób na aktualizację własnych niepewności zgodnie z danymi, które otrzymujemy. Tym sposobem należy aktualizować nasze przekonania, w innym przypadku zawierzamy nasze życie wróżbitom, alchemii lub magii, a nie ***statystyce***.

#### Twierdzenie Bayesa w wersji ciągłej

$D$ - obserwacje
$\mathcal{P} = \{ P(X ; \theta) \}, \theta \in \Theta)$ - rodzina rozkładów prawdopodobieństwa z parametrem $\theta$

Mamy rodzinę rozkładów i zastanawiamy się, które pasują do danych.
- naszymi obserwacjami $D$ mogą być np. punkty na płaszczyźnie
- sądzimy, że niektóre z rozkładów w prawidłowy sposób opisują te punkty
- parametrem $\theta$ może być np. wektor wartości parametrów rozkładu Gaussa

Weźmy do tego wzór Bayesa:

$$
\begin{equation}
	p(\theta \text{ | } D )= \frac{p(D \text{ | } \theta ) \cdot \pi(\theta)}{P(D)} = \frac{p(D \text{ | } \theta ) \cdot \pi(\theta)}{\int_\Theta p(D \text{ | } \theta )\pi(\theta)d\theta} 
\end{equation}
$$

gdzie:
 - $\pi(\theta)$ - *prior*, zakładamy, że nasze dane będą o stosunkowo małej wartości oczekiwanej i małej wariancji
 - $p(D \text{ | } \theta )$ - *wiarygodność*, szansa, że zaobserwujemy punkty pod zadanymi parametrami rozkładu
 - $p(\theta \text{ | } D )$ - *posterior*, to co nas interesuje, czyli który zestaw parametrów (rozkład) wygenerował te dane (punkty)
 - $P(D)$ - *przesłanka za modelem*, szansa zaobserwowania danych, już nie będzie to zwykła suma, a całka po całej $\Theta$ ^a2cb73

Zbierzmy to razem, każdy lubi rysuneczki:

![[bayes_cont.png]]

#### Przykład: jakie jest prawdopodobieństwo wyrzucenia reszki?

Wiemy, że prawdopodobieństwo ma wartości na przedziale $[0,1]$, więc naszą przestrzenią hipotez ($\Theta$) będzie ten przedział, a poszczególnymi hipotezami będą wartości prawdopodobieństwa z tego przedziału ($\theta$). Jest to ciągły przedział, jako że mogą być różne monety i różne opcje, np.:
- szansa na wyrzucenie reszki wynosi 0.95, bo po stronie orła jest przyspawany ciężarek
- szansa na wyrzucenie resztki wynosi 0.5, bo moneta wydaje się całkiem zwykła

Budujemy model:

$$
\begin{equation}
	X \sim Ber(\theta)
\end{equation}
$$

Jest to rozkład zero-jedynkowy z parametrem $\theta$ - szansa na wyrzucenie reszki. (w angielskiej nomenklaturze jest to ***Ber***). Zakładamy, że 1 - reszka, 0 - orzeł. W kontekście modelu bayesowskiego jest to *wiarygodność* - szansa zaobserwowania danych pod zadanymi parametrami. 

Niech obserwacje ($D$), to $N_1$ wyników "reszka" oraz $N_0$ wyników "orzeł". Wówczas wiarygodność naszego modelu to:

$$
\begin{equation}
	p(D \text{ | } \theta ) = \theta^{N_1}(1-\theta)^{N_0}
\end{equation}
$$

Czyli prawdopodobieństwo ujrzenia $N_1$ reszek oraz $N_0$ orłów podczas naszego eksperymentu.

Brakuje nam rozkładu *a priori* dla $\theta$. Dobieranie zarówno rozkładu *a priori* jak i wiarygodności jest ważne, bo inaczej możemy znaleźć się w sytuacji gdzie nie będziemy w stanie policzyć [[#^a2cb73|tej całki]] (np. wykładnicze sumowanie albo funkcja, dla której całka nie istnieje). Wniosek jest taki: "nie kombinuj za bardzo, bo dostaniesz taki model, że się zesrasz". ^96839e

Weźmy *prior* taki, który przypomina wiarygodność (to nie zawsze działa):

$$
\begin{equation}
	\pi(\theta) \propto \theta^a(1-\theta)^b
\end{equation}
$$

gdzie $a$ i $b$ są pewnymi stałymi.

Dlaczego? Bo tak możemy. Nie ma żadnego twierdzenia czy nakazu wyboru danego rozkładu *a priori*. 

Akurat tak się złożyło, że nasz rozkład *a priori* jest znanym rozkładem. Jest to rozkład [***Beta***](https://pl.wikipedia.org/wiki/Rozk%C5%82ad_beta) :

$$
\begin{equation}
	\text{Beta}(\theta \text{ | } \alpha, \beta) =  \frac{1}{Z}\theta^{\alpha-1}(1-\theta)^{\beta-1}
\end{equation}
$$

gdzie $Z$ jest stałą normalizującą.

Spróbujmy to pociągnąć dalej i wyliczyć rozkład *a posteriori*. Przyjmując rozkład Beta, dla takiego rozkładu *a priori* mamy. Policzmy sam licznik rozkładu *a posteriori*, w tym wypadku nie będziemy mieli równości, a proporcjonalność ($\propto$). Jest to iloczyn wiarygodności naszego modelu i rozkładu *a priori*:

$$
\begin{equation}
	p(D \text{ | } \theta ) \propto p(D \text{ | } \theta ) \cdot \text{Beta}(\theta \text{ | } \alpha, \beta)
\end{equation}
$$

Rozwijając te wzory mamy:

$$
\begin{equation}
	\propto \theta^{N_1}(1-\theta)^{N_0} \cdot\theta^{\alpha-1}(1-\theta)^{\beta-1}
\end{equation}
$$
$$
\begin{equation}
	\propto \theta^{N_1+\alpha-1}(1-\theta)^{N_0+\beta-1} 
\end{equation}
$$

Otrzymana postać rozkładu *a posteriori* jest rozkładem Beta z parametrami: $(N_1+\alpha-1)$ oraz $(N_0+\beta-1)$. Wyszło fajnie, bo skoro to znany rozkład, to pewnie całkę ktoś już taką wyliczył, przy tym stała normalizująca też jest znana - ktoś też już to policzył. Jesteśmy zatem w domu. Model, który policzyliśmy nazywa się rozkładem ***Beta-dwumianowym***

Taką sytuację, gdzie postać funkcyjna rozkładów *a priori* i *a posteriori* jest tożsama nazywamy ***sprzężonym rozkładem a priori (conjugate prior)***. 

Przykłady:
Zakładając, że nie wiemy nic o monecie ($Beta(1,1)$, to rozkład jednostajny nad $[0,1]$), po 4 rzutach mamy taką sytuację

![[Pasted image 20240405003426.png]]

Rzucamy dalej, ale ta nasza moneta coś dziwna się robi i reszka wypada coraz częściej

![[Pasted image 20240405003513.png]]

Możemy też założyć, że moneta nie wygląda na uczciwą i jest dziwnie skonstruowana:

![[Pasted image 20240405003649.png]]

Po sporej liczbie rzutów dalej. Co prawda *a priori* założyliśmy, że jest to podróbka, tak nasze obserwacje uaktualniły naszą sytuację i prawdopodobieństwo jest bliższe 0.5. Przy takiej liczbie prób możemy mieć większą pewność, jako że widełki rozkładu się zmniejszyły:

![[Pasted image 20240405003728.png]]

Rozkład a posteriori podsumowuje całą naszą wiedzę o estymowanym parametrze (z perspektywy wnioskowania probabilistycznego).

Dla otrzymanego rozkładu *a posteriori* możemy także wyznaczyć ***przedział wiarygodności (credible interval)***. Czyli gdzie leży najbardziej wiarygodna wartość rozkładu. Możemy wyciąć przedział, w którym mieści 95% masy prawdopodobieństwa. (nie mylić pojęcia z przedziałem ufności!)

![[Pasted image 20240405005033.png]]

Możemy wyznaczyć wartość parametru maksymalizującą gęstość a posteriori. Takie maksimum nazywamy ***estymatą MAP (maximum a posteriori)***. ^09ef62

$$
\begin{equation}
	\theta_{MAP} = \underset{\theta}{\text{arg max }}p(\theta \text{ | } D)
\end{equation}
$$

![[Pasted image 20240405084638.png]]

Załóżmy, że mamy nowe dane i chcemy wyznaczyć dla nich wartość modelu. Innymi słowy, mamy nową obserwację i zastanawiamy się, w jaki sposób nasz model mógłby ją wygenerować. Musiałby ze swojego zbioru danych wywnioskować parametr $\theta$, a następnie z tego parametru wyciągnąć nową obserwację $x$. Rozważając wszystkie możliwości (razem z marginalizacją), czyli całkując po $\Theta$ oraz wykorzystując już wyznaczony rozkład *a posteriori*:

$$
\begin{equation}
	p(x\text{ | } D )= {\int_\Theta p(x \text{ | } \theta)p(\theta \text{ | } D)d\theta} 
\end{equation}
$$

Otrzymany rozkład nazywamy ***rozkładem predyktywnym (posterior predictive distribution)***.

^6894ed
