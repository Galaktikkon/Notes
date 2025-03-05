
Załóżmy, że modelujemy obserwacje takiej postaci:

$$
\begin{equation}
(y, \textbf{x})
\end{equation}
$$

gdzie $y \in \mathbb{R}$ to skalar zwany ***zmienną objaśnianą***, zaś $\textbf{x} \in \mathbb{R}^n$ to wektor ***zmiennych objaśniających***. Czyli przy pomocy wektora $\textbf{x}$ próbujemy przewidzieć (objaśnić) zmienną $y$.
 - możemy sobie wyobrazić, tak że naszymi obserwacjami są punkty, a naszym dopasowaniem będzie prosta
 
![[Pasted image 20240406192757.png]]

Zakładamy, że:
 - zmienna objaśniana zależy liniowo od zmiennych objaśniających
 - zależność ta (jak to w życiu) jest obarczona błędem, który wynika na przykład z niepewności oszacowana $\textbf{x}$.

Możemy to wyrazić w postaci zależności:

$$
\begin{equation}
y = \textbf{w}^{\text{T}}\textbf{x}+b+\epsilon
\end{equation}
$$

gdzie $\textbf{w}$ i $b$ to parametry modelu wyrażające zależność liniową, a $\epsilon$ to zmienna losowa opisująca błąd. Czyli $y$ jest przekształceniem afinicznym $\textbf{x}$, $\textbf{w}$ to wagi, które reprezentują tę prostą w przestrzeni, a $b$ to pewna stała, która odnosi ją od 0. 

Jest to typowy przykład modelu ***regresji liniowej***.

Naszym celem jest wnioskowanie o parametrach $\textbf{w}$ i $b$ tak, byśmy w przyszłości mogli szacować zmienne objaśniane na podstawie zmiennych objaśniających. Czyli znaleźć pewien rozkład prostych określających tę liniową zależność. Innymi słowy: mamy wiele par $(y,\text{x})$ - obserwacje - i na ich podstawie chcemy wnioskować o parametrach $w$ i $b$.

Parametryzację regresji liniowej możemy uprościć (dla prostszej algebry) przyjmując, że do wektora zmiennych objaśniających *"dokładamy"* wartość 1, zaś $b$ parametr włączamy w wektor $\textbf{w}$:

$$
\begin{equation}
\textbf{x} \leftarrow [\textbf{x},1]
\end{equation}
$$
$$
\begin{equation}
\textbf{w} \leftarrow [\textbf{w},b]
\end{equation}
$$

Wtedy:

$$
\begin{equation}
\textbf{w}^{\text{T}} = [\textbf{w}^{\text{T}},b]
\end{equation}
$$
$$
\begin{equation}
[\textbf{w}^{\text{T}},b] \cdot \left[ \begin{matrix}  x \\ 1  \end{matrix} \right] = \textbf{w}^{\text{T}}\textbf{x}+b
\end{equation}
$$

Czyli jest to interesujące nas przekształcenie. Wówczas zależność pomiędzy zmienną objaśnianą, a zmiennymi objaśniającymi przyjmuje postać:

$$
\begin{equation}
y = \textbf{w}^{\text{T}}\textbf{x}+\epsilon
\end{equation}
$$

Jest to zależność czysto liniowa - pozbyliśmy się zależności afinicznej. 
#### Regresja liniowa - interpretacja

Zastanówmy się jaką rolę pełni $\text{w}$ w regresji liniowej. 

W jednowymiarowym przypadku jest to w miarę intuicyjne i proste. Wtedy parametr $\text{w}$ to są dwie wartości np. $a$ i $b$, z czego pierwsza jest współczynnikiem kierunkowym prostej na płaszczyźnie, a druga punktem odcięcia. 

Co w przypadku, gdy mamy do czynienia z wielowymiarową regresją linową, tzn. $x \in {\mathbb{R}}^n$? Wtedy naszym dopasowaniem zamiast prostej, będzie pewna hiperpłaszczyzna, a wektor $\text{w}$ przyjmie rolę wektora wodzącego (wektor prostopadły do płaszczyzny)

![[Pasted image 20240611185656.png]]

Oczywiście w przypadku zmiany wektora wodzącego zmieni się też płaszczyzna, więc naszym zadaniem jest takie zorientowanie wektora $\text{w}$, żeby płaszczyzna dobrze opisywała nasze dane.

![[Pasted image 20240611190149.png]]

#### Regresja liniowa - rozkład błędu

Dosyć istotnym pytaniem wydaje się to jaki rozkład błędu ma nasze dopasowanie. Nie znając go nie bylibyśmy w stanie określić poprawności naszego oszacowania - które proste uznajemy za dobrze opisujące dane. 
- np. jeżeli nasz przyrząd pomiarowy systematycznie zawyża pomiar, to powinniśmy to uwzględnić gdzieś w naszym modelu (np. w parametrze $b$)

Możemy zrobić i zrobimy proste założenie na temat $\epsilon$. Teoretycznie jest tutaj dozwolona fantazja na różnorakie przewidywanie tego parametru, ale jeżeli nasz rozkład wyciągniemy z kapelusza to cytując klasyka: [[Wnioskowanie Bayesowskie#^96839e|"dostaniesz taki model, że się zesrasz"]] - możemy nie znaleźć matematyki, która umożliwi nam nasze obliczenia. Zatem przyjmijmy, że nasz błąd ma rozkład normalny:

$$
\begin{equation}
\epsilon \sim N(0, \sigma^2)
\end{equation}
$$

Póki co na tym kursie rozważamy **tylko** ten przypadek. Robimy dodatkowo drugie mocne założenie (które może być w pewnym sensie ograniczające). Mianowicie zakładamy, że znamy wartość parametru $\sigma$ - ktoś nam powiedział, że ten zardzewiały teleskop to robi takie i takie czary. Jednak często w przypadku np. danych historycznych, mimo że będziemy mogli założyć, że składowe błędu dają rozkład normalny, tak ciężej będzie z określeniem odchylenia standardowego.

Nasze założenia mają na celu "uprościć majcę", która stoi za naszym zadaniem. Później poznamy metody, które nam pozwolą na większą swobodę.

Założenie o tym, że błąd ma rozkład normalny wskazuje na to, że zakładamy że większość błędów będzie skupiona w przedziale $2 \sigma$ lub $3 \sigma$, a na poziomie $5\sigma$ błąd się praktycznie nigdy nie wydarzy. 

![[Pasted image 20240611194649.png]]

To oznacza, że nasz model zakłada sytuację, że w naszych danych raczej nie ma danych odstających (*outliers*). Tylko w przypadku, w którym takie się pojawią np. ktoś źle odczyta dane z tabelki albo cokolwiek nam wejdzie w paradę, to nasz model na to zareaguje - nie dopuści takiego błędu pomiaru i "kojfnie" naszą prostą. (model: "BŁĄD $7\sigma$???, tak nie może być! ")

![[Pasted image 20240611195435.png]]

Żeby zredukować ten problem, można przyjąć inny rozkład z tzw "ciężkimi ogonami" np. rozkład Studenta albo rozkład Laplace'a. Wtedy model będzie bardziej liberalny. (model: "błąd $5\sigma$? PROBLEM?").  Taki odporny model nazywamy ***odporną regresją liniową*** (ang. *robust linear regression*).

#### Regresja liniowa - wnioskowanie

Przyjmijmy, że nasze obserwacje opisuje zbiór:
$$
\begin{equation}
	D = \{(y_1,\text{x}_1), (y_2,\text{x}_2), ..., (y_n,\text{x}_n)\}
\end{equation}
$$
gdzie $y_i$ to zmienna objaśniana, a $\text{x}_i$ to zmienna objaśniająca. 

Jak już ustaliliśmy, chcemy poznać zależność między tymi zmiennymi, czyli oszacowanie $\text{w}$ na podstawie $D$. Rozważymy dwa przypadki:
1. Estymatę punktową (dopasowanie odpowiedniej prostej do punktów) - metoda najmniejszych kwadratów
2. Model Bayesowski dla regresji liniowej (niepewność regresji liniowej)

##### Estymata punktowa

W celu realizacji tego zadania, musimy przyjąć jakieś kryterium, które umożliwi nam odpowiednie dopasowanie. Kryterium z którego skorzystamy, będzie ***metoda największej wiarygodności*** (ang. *maximum likelihood estimation*, *maximum likelihood learning*; *MLE*). Czyli zamiast liczyć rozkład a posteriori, szukamy takich parametrów, które maksymalizują wiarygodność. Jest to dosyć powszechne kryterium w obecnym uczeniu maszynowym.

Zakładając, że
$$
\begin{equation}
\epsilon \sim N(0, \sigma^2)
\end{equation}
$$
To oznacza, że prawdopodobieństwo zaobserwowania pojedynczego punktu dla określonej wartości $\text{w}$ jest określona jako gęstość w rozkładzie normalnym:

$$
\begin{equation}
	{N(y_i \text{ | } \text{w}^{\text{T}}\text{x}_i, \sigma^2)}
\end{equation}
$$

Co lepiej widać po zobrazowaniu:

![[Pasted image 20240611214321.png]]

Jeżeli założymy, że nasze obserwacje są ***i.i.d.*** (independent and identically distributed), czyli że są wzajemnie niezależne oraz mają ten sam rozkład, to będzie z niezależności zdarzeń (po prostu mnożymy te prawdopodobieństwa) funkcja wiarygodności będzie miała postać:
$$
\begin{equation}
	p(D \text{ | } \text{w}) = \prod_{i=1}^{n}{N(y_i \text{ | } \text{w}^{\text{T}}\text{x}_i, \sigma^2)}
\end{equation}
$$
W metodzie największej wiarygodności jako estymatę parametru przyjmujemy wartość maksymalizującą wiarygodność:
$$
\begin{equation}
	W_{MLE} = \underset{\text{w}}{\text{arg max }}\prod_{i=1}^{n}{N(y_i \text{ | } \text{w}^{\text{T}}\text{x}_i, \sigma^2)}
\end{equation}
$$
Czyli wybieramy takie parametry $\text{w}$ dla których prawdopodobieństwo zaobserwowania danych pod warunkiem tak dobranych parametrów jest największe. 

Spróbujmy wyliczyć te parametry. Z punktu widzenia numerycznego optymalizacja bardzo małych (lub bardzo dużych) wartości może być ciężka. W naszym przypadku czynniki prawdopodobieństwa będą małe (bardzo małe), a jeżeli jeden czynnik jest mały, to wpływa to znacząco na całość działania. Możemy zatem zmienić strategię i zamiast iloczynu liczyć sumę - obłożymy to logarytmem. Dodatkowo wprowadzimy konwencję (dosyć popularną), że zamiast maksymalizować sumę, to będziemy chcieli liczyć minimum (np. jako koszt modelu). W takim wypadku nasza funkcja wiarygodności wygląda następująco:

$$
\begin{equation}
	\mathcal{L}(\text{w}) = -\log{p(D \text{ | } \text{w})}
\end{equation}
$$

Jest to ***zanegowana logarytmiczna funkcja wiarygodności*** (ang. *negative log likelihood function*). 

$$
\begin{equation}
	W_{MLE} = \underset{\text{w}}{\text{arg min }}\mathcal{L}(\text{w})
\end{equation}
$$
Co możemy rozpisać jako:

$$
\begin{equation}
	\mathcal{L}(\text{w}) = -\log{p(D \text{ | } \text{w})} = -\log \prod_{i=1}^{n}{N(y_i \text{ | } \text{w}^{\text{T}}\text{x}_i, \sigma^2)} = 
\end{equation}
$$
Jak wcześniej wspominaliśmy logarytm zamieni nam iloczyn na sumę:
$$
\begin{equation}
	 = \left(2\sigma^2\right)^{-1}\sum_{i=0}^{n}\left(y_i - \text{w}^{\text{T}}\text{x}_i\right)^2 + const
\end{equation}
$$
gdzie $const$ to suma logarytmów czynników normalizujących, która nie zależy od $\text{w}$, więc możemy ją pominąć w optymalizacji. Analogicznie czynnik $\left(2\sigma^2\right)^{-1}$ (który był wspólny dla składników, więc go wyłączyliśmy przed sumę) też nie zależy od $\text{w}$. ^124cce

Zatem poszukujemy minimum:

$$
\begin{equation}
	W_{MLE} = \underset{\text{w}}{\text{arg min }}\frac{1}{2}\sum_{i=0}^{n}\left(y_i - \text{w}^{\text{T}}\text{x}_i\right)^2
\end{equation}
$$

Co bardziej powszechnie jest znane jako ***metoda najmniejszych kwadratów*** (*ang. ordinary least squares* – OLS), bo minimalizujemy sumę kwadratów błędów przybliżenia. ^2ce7c4

Teraz pozostaje wyznaczyć wzór. Przyjmijmy oznaczenia:

$$
\begin{equation}
\textbf{X} \ = \left[ \begin{matrix} \text{x}^\text{T}_1 \\ \text{x}^\text{T}_2 \\ \vdots \\ \text{x}^\text{T}_n
\end{matrix} \right], \textbf{y} \ = \left[ \begin{matrix} y_1 \\ y_2 \\ \vdots \\ y_l
\end{matrix} \right]
\end{equation}
$$

Przy tych oznaczeniach nasz problem optymalizacyjny przyjmuje postać (z magii algebry - "elementarna algebra macierzowa"):

$$
\begin{equation}
	W_{MLE} = \underset{\text{w}}{\text{arg min }}\frac{1}{2}(\textbf{y} - \textbf{Xw})^{\text{T}}(\textbf{y} - \textbf{Xw})
\end{equation}
$$

Aby policzyć minimum tego wyrażenia policzymy pochodną względem $\text{w}$ (znów magia algebry - pochodne i formy kwadratowe):

$$
\frac{\partial}{\partial\text{w}}\left[\frac{1}{2}(\textbf{y} - \textbf{Xw})^{\text{T}}(\textbf{y} - \textbf{Xw})\right] = 
$$
$$
\frac{\partial}{\partial\text{w}}\left[
\frac{1}{2}\textbf{y}^\text{T}\textbf{y} - \frac{1}{2}\textbf{y}^\text{T}\textbf{Xw} - \frac{1}{2}(\textbf{Xw})^\text{T}\textbf{y} + \frac{1}{2}(\textbf{Xw})^\text{T}(\textbf{Xw})
\right] = 
$$
$$
\frac{\partial}{\partial\text{w}}
\left[
	\frac{1}{2}\textbf{w}^{\text{T}}(\textbf{X}^{\text{T}}\textbf{X})\text{w} - \text{w}^{\text{T}}(\textbf{X}^{\text{T}}\text{y})
\right] = 
$$
$$
= (\textbf{X}^{\text{T}}\textbf{X})\text{w} - \textbf{X}^{\text{T}}\text{y}
$$

W minimum pochodna zanika, co daje:

$$
(\textbf{X}^{\text{T}}\textbf{X})\text{w}_{MLE} - \textbf{X}^{\text{T}}\text{y} = 0
$$
zatem:

$$
\text{w}_{MLE} = (\textbf{X}^{\text{T}}\textbf{X})^{-1}\textbf{X}^{\text{T}}\text{y}
$$

^a47188

Zwróćmy uwagę, że estymata nie zależy od skali błędu ($\sigma$), co ma sens, bo założyliśmy uproszczenie - zignorowaliśmy niepewność $\text{w}$.

##### Bayesowska regresja liniowa

Teraz rozważmy przypadek gdzie nie ignorujemy niepewności $\text{w}$. Możemy ją wyrazić jako rozkład a posteriori $p(\text{w | }D)$. Do bayesowskiego wnioskowania potrzebujemy rozkładu a priori oraz wiarygodności. Wiarygodność już wyprowadzaliśmy jest to:

$$
y_i \text{ | w} \sim N(\text{w}^\text{T}\text{x}_i, \sigma^2)
$$

dla całego zbioru danych (zaobserwowanie całego wektora danych $\textbf{y}$):

$$
\textbf{y | w} \sim N(\textbf{Xw}, \sigma^2\textbf{I})
$$

Zostaje pytanie jaki przyjąć prior dla $\text{w}$? Aby poprzednie wykłady nie poszły w las (modele Gaussowskie), bo tam już mamy gotowe narzędzia, to weźmiemy prior jako rozkład normalny, wtedy otrzymamy instancję liniowego modelu Gaussowskiego:

$$
\textbf{w} \sim N(\mu_0, \Sigma_0)
$$
$$
\textbf{y | w} \sim N(\textbf{Xw}, \sigma^2\textbf{I})
$$

Zatem:

$$
\textbf{w | D} \sim N(\mu_n, \Sigma_n)
$$

gdzie:

$$
\Sigma_n = 
\left[
	\Sigma_0^{-1}+\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{X}
\right]^{-1}
$$
$$
\mu_n = \Sigma_n
	\left[
		\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{y}+\Sigma_0^{-1}\mu_0 
	\right]
$$
Nazwy tych parametrów ($\Sigma_n$, itd.) oznaczają wartości odpowiednich parametrów po uwzględnieniu $n$ obserwacji.

##### Rozkład predykcyjny

Mamy już rozkład z posteriori nad $\text{w}$. Rozważmy przypadek w którym dla takiego rozkładu chcemy wnioskować rozkład nad zmienną objaśnianą $y$ dla nowego wektora zmiennych $\text{x}$ - chcemy dokonać predykcji.

![[Pasted image 20240612000652.png]]

Więc interesuje nas [[Wnioskowanie Bayesowskie#^6894ed|rozkład predykcyjny a posteriori]] - $p(y \text{ | x},D )$. Możemy go opisać w następujący sposób:

$$
\textbf{w | D} \sim N(\mu_n, \Sigma_n)
$$
$$
y \text{ | x, w} \sim N(\text{x}^\text{T}\text{w}, \sigma^2)
$$
Ponownie jest to liniowy model Gaussowski (*ahh shit here we go again*). Jednak rolę rozkładu a priori pełni tu posterior $p(\text{w | }D)$. Skoro tak, [[Modele Gaussowskie#Wnioskowanie w łącznym rozkładzie MVN|to wiemy]], że rozkład łączny $p(y\text{, w | x, }D)$ jest rozkładem normalnym. W takim wypadku interesuje nas rozkład brzegowy $p(y \text{ | x},D )$, który też jest rozkładem normalnym. Wystarczy nam dobrać się do parametrów tego rozkładu.

Wartość oczekiwaną możemy wyznaczyć w prosty sposób:

$$
\mathbb{E}[y\text{ | x, }D] = \mathbb{E}[\text{w}^\text{T}\text{x}+ \epsilon \text{ | }D] =
$$

Z liniowości wartości oczekiwanej:

$$
= \mathbb{E}[\text{w}^\text{T}\text{x}\text{ | }D] + \mathbb{E}[\epsilon]
$$

Błąd ma wartość oczekiwaną równą 0:

$$
= \mathbb{E}[\text{w}^\text{T}\text{ | }D]\text{ x} + 0
$$
$$
= \mu_n^{\text{T}}\text{x}
$$

Wariancję $y$ możemy wyprowadzić poprzez odwrócenie macierzy precyzji dla rozkładu łącznego -  u nas dla rozkładu $p(y\text{, w | x, }D)$. Tych obliczeń jest (podobno) sporo i to sama algebra. Na koniec przy wyznaczonej macierzy odczytujemy wartość kowariancji $y$, będzie to prawa dolna kratka macierzy. Dostajemy (z magii algebry):

$$
	\text{Var}[y\text{ | x, }D] = \sigma^2 + \text{x}^{\text{T}}\Sigma_n\text{x}
$$ 
Podsumujmy:

$$
y \text{ | x, w} \sim N(\mu_y, \sigma^2_y)
$$
gdzie:

$$
	\mu_y = \mu_n^{\text{T}}\text{x}
$$
$$
\sigma^2_y = \sigma^2 + \text{x}^{\text{T}}\Sigma_n\text{x}
$$
