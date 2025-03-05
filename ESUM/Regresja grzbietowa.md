Rozważmy [[Regresja liniowa#Bayesowska regresja liniowa|Bayesowską regresję liniową]], w której prior ma postać:
$$
\text{w} \sim N(0, \tau^2I)
$$
Czyli tak jak wcześniej zakładaliśmy w ogólności, że znamy pewną wartość oczekiwaną $\mu_0$ oraz odchylenie standardowe jako macierz kowariancji $\Sigma_0$, tak teraz przyjmiemy wartość oczekiwaną równą 0, a macierz kowariancji jest diagonalna, gdzie ich skala jest proporcjonalna do $\tau$ - są niezależne i mają amplitudy proporcjonalne do jakiejś małej liczby.

Wiarygodność pozostaje bez zmian:

$$
\textbf{y | w} \sim N(\textbf{Xw}, \sigma^2\textbf{I})
$$

Interesuje nas [[Wnioskowanie Bayesowskie#^09ef62|estymata maksimum a posteriori]] parametrów $\text{w}$. Na podstawie [[Wnioskowanie Bayesowskie#Twierdzenie Bayesa w wersji ciągłej|wzoru Bayesa w wersji ciągłej]] - sprowadza się to to maksymalizacji licznika, jako że całka jest czynnikiem normalizującym (stąd znak proporcjonalności ($\propto$)):

$$
p(\text{w} \text{ | } D) \propto p(\text{w | 0, } \tau^2I)p(\text{y | Xw, }\sigma^2I)
$$

to estymatą MAP jest:

$$
\begin{equation}
	W_{MAP} = \underset{\text{w}}{\text{arg max }}\left[p(\text{w | 0, } \tau^2I)p(\text{y | Xw, }\sigma^2I)\right]
\end{equation}
$$

Aby pozbyć się exponent z gęstości rozkładu normalnego skorzystamy z monotoniczności funkcji logarytmicznej (maksymalizując logarytm wyrażenia, otrzymamy tę samą wartość $\text{w}$) otrzymujemy:

$$
\begin{equation}
	W_{MAP} = \underset{\text{w}}{\text{arg max }}\left[\log{p(\text{w | 0, } \tau^2I)}+\log{p(\text{y | Xw, }\sigma^2I)}\right]
\end{equation}
$$

Stałe normalizujące sprowadzą się do jednej wartości $const$, która zależy od kowariancji - nie zależy od $\text{w}$:

$$
\begin{equation}
	=\underset{\text{w}}{\text{arg max }}\left[-\tau^{-2}\text{w}^{\text{T}}\text{w}-\sigma^{-2}(\text{y}-\text{Xw})^{\text{T}}(\text{y}-\text{Xw}) + const)\right]
\end{equation}
$$

Szukając maksimum zanegowanych członów, możemy równie dobrze znaleźć minimum członów bez negacji:

$$
\begin{equation}
	=\underset{\text{w}}{\text{arg min }}\left[\tau^{-2}\text{w}^{\text{T}}\text{w}+\sigma^{-2}(\text{y}-\text{Xw})^{\text{T}}(\text{y}-\text{Xw}\right]
\end{equation}
$$
Dodatkowo wyrażenie w nawiasach pomnożyć przez $\sigma^2$ - nie wpłynie to na położenie minimum.
 - Bo $\sigma^2$ to dodatnia stała
Zatem estymata MAP w tym modelu przyjmuje ostatecznie postać:

$$
\begin{equation}
	W_{MAP}=\underset{\text{w}}{\text{arg min }}\left[(\text{y}-\text{Xw})^{\text{T}}(\text{y}-\text{Xw})+\frac{\sigma^2}{\tau^2}\text{w}^{\text{T}}\text{w}\right]
\end{equation}
$$

Wiemy, że $\text{w}^{\text{T}}\text{w}$ to kwadrat normy wektora $\text{w} = ||\text{w}||^2$

$$
\begin{equation}
	W_{MAP}=\underset{\text{w}}{\text{arg min }}\left[(\text{y}-\text{Xw})^{\text{T}}(\text{y}-\text{Xw})+\frac{\sigma^2}{\tau^2}||\text{w}||^2\right]
\end{equation}
$$

Zauważmy, że sprowadza się to znalezienia rozwiązania, które będzie minimalizowało błąd kwadratowy między zmiennymi objaśnianymi, przekształceniem afinicznym zmiennych objaśnianych (czyli po prostu [[Regresja liniowa#^2ce7c4|OLS]]) - pierwszy człon, jednak zarazem chcemy używam jak najmniejszych wag $\text{w}$ - drugi człon (***człon regulujący***). Mamy w tym przypadku tak zwaną regularyzowaną regresję liniową. Regulujemy rozwiązanie w taki sposób, aby rozwiązanie nie było zbyt duże.

Zapiszmy to w zgrabniejszej, bardziej eleganckiej wersji:

$$
\begin{equation}
	W_{RR}=\underset{\text{w}}{\text{arg min }}\left[(\text{y}-\text{Xw})^{\text{T}}(\text{y}-\text{Xw})+\gamma||\text{w}||^2\right]
\end{equation}
$$

gdzie $\gamma>0$ to stała regularyzująca (żeby nie pisać cały czas $\frac{\sigma^2}{\tau^2}$). Jest to tak zwana ***regresja grzbietowa*** (ang. *ridge regression*) - stąd nazwa estymaty $W_{RR}$.

W sytuacji idealnego dopasowania, które bierze pod uwagę punkty odstające, możemy dołożyć człon regularyzujący, który wymusi stosowanie stosunkowo małych wag - zakładamy, że nasza zależność nie używa bardzo dużych, "dziwnych liczb".

![[Pasted image 20240613200156.png]]

Ten model regresji posiada rozwiązanie w postaci zamkniętej - możemy posłużyć się już znanymi nam narzędziami. Rozwiązaniem jest estymata MAP dla Bayesowskiej regresji liniowej z priorem postaci: 

$$
\text{w} \sim N(0, \tau^2I)
$$

Wiemy, że skoro prior i wiarygodność są rozkładami normalnymi, to posterior również jest rozkładem normalnym, a w rozkładzie normalnym punktem o największej gęstości prawdopodobieństwa (modą) jest wartość oczekiwana, więc z [[Regresja liniowa#Bayesowska regresja liniowa|Bayesowskiej regresji liniowej]]:

$$
\begin{equation}
	W_{RR}= \mu_n = \Sigma_n
	\left[
		\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{y}+\Sigma_0^{-1}\mu_0 
	\right]
\end{equation}
$$
$$
\begin{equation}
	= 
	\left[
		\Sigma_0^{-1}+\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{X}
	\right]^{-1} 
	\left[
		\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{y}+\Sigma_0^{-1}\mu_0 
	\right]
\end{equation}
$$
Wystarczy, że rozwikłamy to dla parametrów naszego modelu regresji grzbietowej:

$$
\begin{equation}
	W_{RR}= 
	\left[
		\Sigma_0^{-1}+\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{X}
	\right]^{-1} 
	\left[
		\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{y}+\Sigma_0^{-1}\mu_0 
	\right]
\end{equation}
$$
$$
\begin{equation}
	= 
	\left[
		\frac{1}{\tau^2}\text{I}+\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{X}
	\right]^{-1} 
	\left[
		\frac{1}{\sigma^2}\textbf{X}^\text{T}\textbf{y}+\frac{1}{\tau^2}\text{I} \cdot 0 
	\right]
\end{equation}
$$
Z magii algebry:
$$
\begin{equation}
	= 
	\left[
		\frac{\sigma^2}{\tau^2}\text{I}+\textbf{X}^\text{T}\textbf{X}
	\right]^{-1} 
	\textbf{X}^\text{T}\textbf{y}
\end{equation}
$$
Używając naszego hiper-parametru $\gamma$:

$$
\begin{equation}
	W_{RR}= 
	\left[
		\gamma\text{I}+\textbf{X}^\text{T}\textbf{X}
	\right]^{-1} 
	\textbf{X}^\text{T}\textbf{y}
\end{equation}
$$

Czyli do [[Regresja liniowa#^a47188|wzoru]] z OLS dochodzi czynnik regularyzujący $\gamma\text{I}$.
