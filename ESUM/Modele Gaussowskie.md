Jest to ważna klasa modeli bayesowskich. Jak łatwo się domyślić są zbudowane na wielowymiarowym rozkładzie Gaussowskim. Przy wykorzystaniu analityki (i może kartki dla masochistów), możemy wnioskować *[[Wnioskowanie Bayesowskie#^3eafd3|rozkłady a posteriori]]* (czy też *[[Wnioskowanie Bayesowskie#^6894ed|rozkłady predykcyjne a posteriori]]*) w postaci jawnej. Przyda się to też w [[Regresja liniowa|regresji liniowej]] czy też [[Wprowadzenie do procesów Gaussowskich#Procesy Gaussowskie w regresji|regresji procesem Gaussowskim]].


#### Powtórka z rachunku prawdopodobieństwa

Jeśli $x=[x_1, x_2, x_3,..., x_d]$ ma wielowymiarowy rozkład normalny (***Multivariate Normal distribution – MVN***) z wartością oczekiwaną i macierzą kowariancji to:
- w jednowymiarowym ujęciu opisuje on krzywe dzwonowe, w wielowymiarowym też, ale te krzywe są nad płaszczyzną

$$
\begin{equation}
p(x)  = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}}\text{exp}\left[ -\frac{1}{2}(\textbf{x} - \textbf{$\mu$})^\text{T}\Sigma^{-1}(\textbf{x} - \textbf{$\mu$}) \right]
\end{equation}
$$

Co możemy wyrazić skrótowo:

$$
\begin{equation}
\textbf{x} \sim N(\textbf{$\mu$}, \textbf{$\Sigma$})
\end{equation}
$$
gdzie $\mu$ to wektor wartości oczekiwanej, a $\Sigma$ to macierz kowariancji (rozrzut danych nie musi być równoległy do osi - te krzywe mogą być krzywe).

![[Pasted image 20240405092848.png]]

Ponadto wyróżniamy odwrotność macierzy kowariancji, którą nazywamy ***macierzą precyzji***

$$
\begin{equation}
\Lambda:=\Sigma^{-1}
\end{equation}
$$

Podczas gdy macierz kowariancji mówi o rozrzucie danych (jak bardzo są one rozrzucone), macierz precyzji mówi o skupieniu danych. Jeśli rośnie precyzja, to dane są coraz bardziej skupione.

#### Wnioskowanie w łącznym rozkładzie MVN

Weźmy dwa wielowymiarowe wektory:

$$
\begin{equation}
\textbf{x} \ = \left[ \begin{matrix} x_1 \\ x_2 \\ \vdots \\ x_k
\end{matrix} \right], \textbf{y} \ = \left[ \begin{matrix} y_1 \\ y_2 \\ \vdots \\ y_l
\end{matrix} \right],
\end{equation}
$$
Mają one różne liczby wymiarów, załóżmy dodatkowo, że są to zmienne losowe (wyniki różnej liczby pomiarów).

Niech $\textbf{x}$ i $\textbf{y}$ łącznie mają rozkład normalny:

$$
\begin{equation}
\left[ \begin{matrix} \textbf{x} \\ \textbf{y} 
\end{matrix} \right] \sim N(\textbf{$\mu$}, \textbf{$\Sigma$})
\end{equation}
$$

Jest to ***wektor kratkowy***, czyli sklejenie dwóch wektorów (***macierz kratkowa***). 

> [!important] 
> Jeżeli dwie zmienne mają łącznie rozkład normalny, to osobno też mają rozkład normalny.

^e2acc7

Parametrami naszego rozkładu są:

$$
\begin{equation}
\mu \ = \left[ \begin{matrix} \mu_x \\ \mu_y
\end{matrix} \right], \Sigma\ = \left[ \begin{matrix} \Sigma_{\textbf{xx}} & \Sigma_{\textbf{xy}}\\ \Sigma_{\textbf{yx}} & \Sigma_{\textbf{yy}}, 
\end{matrix} \right], \Lambda = \Sigma^{-1} = \left[ \begin{matrix} \Lambda_{\textbf{xx}} & \Lambda_{\textbf{xy}}\\ \Lambda_{\textbf{yx}} & \Lambda_{\textbf{yy}}, 
\end{matrix} \right]
\end{equation}
$$
gdzie kowariancja $\textbf{x}$ z $\textbf{x}$ to $\Sigma_{\textbf{xx}}$. Analogicznie macierz precyzji.

Wówczas rozkłady brzegowe [[#^e2acc7|są rozkładami normalnymi]] (wystarczy "wymazać" nieinteresujące nas zmienne):

$$
\begin{equation}
\textbf{x} \sim N(\textbf{$\mu_{\textbf{x}}$}, \textbf{$\Sigma_{\textbf{xx}}$})
\end{equation}
$$

$$
\begin{equation}
\textbf{y} \sim N(\textbf{$\mu_{\textbf{y}}$}, \textbf{$\Sigma_{\textbf{yy}}$})
\end{equation}
$$
Ponadto rozkłady warunkowe też są rozkładami normalnymi:

$$
\begin{equation}
\textbf{x | y} \sim N(\textbf{$\mu_{\textbf{x | y}}$}, \textbf{$\Sigma_{\textbf{x | y}}$})
\end{equation}
$$
gdzie (te wzory są symetryczne):
$$
\begin{equation}
\Sigma_{\textbf{x | y}} = \Sigma_{\textbf{xx}}-(\Sigma_{\textbf{xy}}\Sigma_{\textbf{yy}})^{-1}\Sigma_{\textbf{yx}}
\end{equation}
$$
$$
\begin{equation}
= \Lambda_{\textbf{xx}}^{-1}
\end{equation}
$$

$$
\begin{equation}
\mu_{\textbf{x | y}} = \mu_{\textbf{x}}-(\Sigma_{\textbf{xy}}\Sigma_{\textbf{yy}})^{-1}(\textbf{y}-\mu_\textbf{y})
\end{equation}
$$
$$
\begin{equation}
= \Sigma_{\textbf{x | y}} (\Lambda_{\textbf{xx}}\mu_\text{x}-\Lambda_{xy}(\textbf{y}-\mu_{\textbf{y}})
\end{equation}
$$
#### Liniowe Modele Gaussowskie

Interesuje nas następujący problem wnioskowania, który chcielibyśmy rozwiązać przy pomocy metod bayesowskich. 

Niech $\text{x}=[x_1, x_2, x_3,..., x_k]^T$ będzie pewną zmienną ukrytą (nieobserwowaną),
- np. odległość do jakiegoś obiektu (gwiazdy)

zaś:
$$
\begin{equation}
\textbf{y}=\textbf{Ax }+ \textbf{b} + \textbf{$\epsilon$}
\end{equation}
$$
jej pomiarem.
- nasz teleskop nie mierzy odległości wprost, mierzy ***afiniczne*** przekształcenie $\textbf{x}$. Po co? Wtedy nasz model jest bardziej ogólny, np. w wypadku gdyby parametry przyrządu mierniczego były inne - ustalamy w oparciu o nie $\textbf{A}$ i $\textbf{b}$.

Innymi słowy, mamy pewną wartość, którą chcemy poznać, ale nie potrafimy zmierzyć jej wprost. W naszym wypadku $\textbf{y}$ jest zmienną obserwowaną - czyli nasze dane.

Oczywiście świat jest ***nieprzewidywalny***, więc nigdy nasz pomiar nie będzie po prostu przekształceniem. Dlatego zakładamy, że jest dokonany ze (znaną) precyzją $\Sigma_\textbf{y}^{-1}$ :

$$
\begin{equation}
\textbf{$\epsilon$} \sim N(\textbf{0}, \textbf{$\Sigma_{\textbf{y}}$})
\end{equation}
$$

^879c4f

Błąd jest przypadkowy, stąd wartość oczekiwana jest równa 0. Wiemy też o skali błędu, co sprowadza się do wiedzy o macierzy kowariancji błędu.

Przy tych założeniach wiarygodność ma postać:
$$
\begin{equation}
p(\textbf{y | x}) = N(\textbf{Ax }+ \textbf{b}, \textbf{$\Sigma_{\textbf{y}}$})
\end{equation}
$$
Jest to rozkład normalny, ponieważ zakładamy, że [[#^879c4f|błąd ma rozkład normalny]], którego wartość oczekiwana wynosiła 0, co oznacza, że dla konkretnej wartości $\textbf{x}$ otrzymamy pomiar o średniej wartości $\textbf{Ax }+ \textbf{b}$. Gdzie rozrzut będzie równy macierzy kowariancji równej błędowi pomiarowemu.

![[Pasted image 20240406145905.png]]

Interesuje nas rozkład *a posteriori* $p(\textbf{x | y})$, ale brakuje nam rozkładu *a priori*. Zakładamy, że jest on dany jako rozkład normalny:
$$
\begin{equation}
\textbf{x} \sim N(\textbf{$\mu_{\textbf{x}}$}, \textbf{$\Sigma_{\textbf{x}}$})
\end{equation}
$$
W tym momencie mamy już wszystkie potrzebne parametry naszego modelu. Tak skonstruowany model nazywamy ***liniowym modelem Gaussowskim (Linear Gaussian Model)***.

Zbierzmy nasze informacje w jednym miejscu. Nasz model jest postaci:

$$
\begin{equation}
\textbf{x} \sim N(\textbf{$\mu_{\textbf{x}}$}, \textbf{$\Sigma_{\textbf{x}}$})
\end{equation}
$$
$$
\begin{equation}
\textbf{y | x} \sim N(\textbf{Ax }+ \textbf{b}, \textbf{$\Sigma_{\textbf{y}}$})
\end{equation}
$$
Wówczas korzystając z rozkładu warunkowego otrzymujemy rozkład *a posteriori*:
$$
\begin{equation}
\textbf{x | y} \sim N(\textbf{$\mu_{\textbf{x | y}}$}, \textbf{$\Sigma_{\textbf{x | y}}$})
\end{equation}
$$
gdzie:
$$
\begin{equation}
\textbf{$\Sigma_{\textbf{x | y}}$} = \left[ \Sigma_\textbf{x}^{-1}+\textbf{A}^\text{T}\Sigma_\textbf{y}^{-1}\textbf{A} \right]^{-1}
\end{equation}
$$
$$
\begin{equation}
\textbf{$\mu_{\textbf{x | y}}$} = \textbf{$\Sigma_{\textbf{x | y}}$} \left[ \textbf{A}^{\text{T}}\Sigma_\textbf{y}^{-1}(\textbf{y}-\textbf{b})+\Sigma_\textbf{x}^{-1}\textbf{$\mu_\textbf{x}$} \right]
\end{equation}
$$
