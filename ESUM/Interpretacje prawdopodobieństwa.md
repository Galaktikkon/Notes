W ujęciu matematycznym to wartość miary probabilistycznej (rozkładu prawdopodobieństwa) w przestrzeni Ω z σ-algebrą zdarzeń Σ. 
$$
\begin{equation}
\text{P}:\Sigma \longrightarrow [0,1]
\end{equation}
$$
Musi ona spełniać aksjomaty:
1. $P(\Omega)=1$
2. Jeśli zdarzenia $A_1, A_2,...$ (dla $A_i \in \Sigma)$ parami wzajemnie się wykluczają, czyli $A_i \cap A_j = \emptyset$ dla dowolnych $i,j \in \mathbb{N}, i\neq j$ , to
$$
\begin{equation}
P(\bigcup_{i=1}^{\infty}A_i)=\sum_{i=1}^{\infty}P(A_i)
\end{equation}
$$
Jednak taka definicja nie mówi nic o interpretacji wartości rozkładu prawdopodobieństwa. 

# Interpretacja częstościowa (częstotliwościowa)

Prawdopodobieństwo zdarzenia $A$, to częstość realizacji (odsetek zdarzeń) $A$ w dużej liczbie prób. ^4e701b

```ad-przyklady
 - Prawdopodobieństwo reszki to częstość wyrzucenia reszki dużej liczbie rzutów monetą.
 - Prawdopodobieństwo niewypłacalności kredytobiorcy to odsetek umów rozwiązanych z powodu niewypłacalności w dużej puli (określonych) umów kredytowych.
- Prawdopodobieństwo mutacji ABC to częstość występowania ABC w ogóle populacji.

Jednak dla wielu problemów, które można napotkać, taka definicja nie jest użyteczna:
 - Jakie jest prawdopodobieństwo, że jutro będzie padał deszcz?
	 - Niestety nie jest to możliwe, aby jutro wystąpiło ***dużą*** liczbę razy.
- Jakie jest prawdopodobieństwo, że partia $A$ wygra najbliższe wybory parlamentarne?
- Jakie jest prawdopodobieństwo, że do 2050r. globalna przeciętna temperatura wzrośnie o 1ºC?
	- Wzrost temperatury to unikalne zdarzenie, które może się wydarzyć lub nie.

Co więcej, pojęcie dużej liczby prób traci sens w wielu typowych problemach uczenia maszynowego:
 - Jakie jest prawdopodobieństwo, że otrzymana wiadomość to spam?
	 - Tę jedną konkretną wiadomość możemy otrzymać tylko raz
 - Jakie jest prawdopodobieństwo, że obiekt wskazany na obrazie to pieszy?
	 - Podobnie jak wyżej jest to jedna konkretna obserwacja.

Zatem szukamy definicji prawdopodobieństwa niezasadzonej w pojęciu wielu prób.
```

# Interpretacja Bayesowska

Prawdopodobieństwo to miara niepewności co do rezultatu określonego zdarzenia, natury badanego obiektu, przyczyn określonego wydarzenia, itd.

Prawdopodobieństwo to miara naszych przekonań/oczekiwań co do rezultatu określonego zdarzenia, natury badanego obiektu, przyczyn określonego wydarzenia, itd.

##### Przykłady
 - Nasza niepewność co do tego, że jutro będzie burza wynosi 0,3
 - Jesteśmy przekonani, że jutro na 99% będzie padał deszcz