# Bisekcja
## Opis algorytmu
Umożliwia on znajdowanie miejsca zerowego funkcji w danym przedziale. Opiera się na twierdzeniu:
#### Bolzana-Cauchy’ego:
> Jeżeli funkcja ciągła f(x) ma na końcach przedziału domkniętego wartości różnych znaków, to wewnątrz tego przedziału, 
> istnieje co najmniej jeden pierwiastek równania f(x)=0.
#### Założenia:
- Funkcja f(x) jest ciągła w podanym przedziale.
- Funkcja f(x) przyjmuje na krańcach przedziału wartości różnych znaków.

Algorytm wykorzystuje te fakty i dzieli podany przedział na połowę, następnie sprawdza, w którym z tych dwóch przedziałów wartości funkcji dla jego krańców są różnych znaków, ponieważ to w nim znajduje się szukane przez nas miejsce zerowe. Cykl ten powtarza się aż do uzyskania przez nas odpowiedniej precyzji.
#### Analiza
Ilość iteracji jaką algortm wykona zależy wyłącznie od podanego przedziału i precyzji, a wyraża się zależnością:
![](https://latex.codecogs.com/gif.latex?\frac{|X1&space;-&space;X2|}{P}&space;\leq&space;2^{n},&space;n\epsilon&space;\mathbb{N},&space;P\epsilon&space;\left&space;(&space;0,&space;\infty&space;\right&space;),&space;X1\epsilon\mathbb{R},&space;X2\epsilon\mathbb{R}). Gdzie P to Precyzja z jaką szukamy miejsca zerowego, X1 lewa, a X2 prawa granica przedziału w którym jest miejsce zerowe, a n jest liczbą iteracji jaką algorytm wykona. Wzór ten sprowadza się do postaci:![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2\tfrac{|X1-X2|}{P}), przy czym n zaokrąglamy w górę do najbliższej liczby naturalnej. Jak łatwo zauważyć z tego wzoru, algorytm wykona się przynajmniej raz dla: ![](https://latex.codecogs.com/gif.latex?|X1-X2|&space;>&space;P).
### Schemat blokowy
![alt text](https://github.com/finloop/biselect/blob/master/Bisekcja.png)
### Kod algorytmu
```c
// Algorytm bisekcji.
// Parametry:
// x1 lewa granica przedziału
// x2 prawa granica przedziału
// getval funkcja na której operuje algorytm (dowolna funkcja która zwraca double
// przyjmuje jeden argument double)
// Przykład: double root = bisection_with_precision(M_PI_2,13/10*M_PI , bsin);
double bisection_with_precision(double x1, double x2, fun getval, double precision)
{
    // Sprawdzenie czy osiągneliśmy wymaganą precyzję
    printf("  %-10s%-10s%-10s%-10s\n", "Iteracja", "L", "P", "Precyzja");
    int i = 0;
    printf("  %-10d%-10f%-10f%-10f\n", i, x1, x2, fabs(x1-x2));
    while(fabs(x1-x2) > precision)
    {
        // Obliczenie warości funkcji dla granic i środka przedziału
        double v_x1 = getval(x1);
        double s = (x1+x2)/2.0;
        double v_s = getval(s);

        // Sprawdzam czy iloczyn lewej granicy i środka jest różnych znaków
        if(v_x1*v_s > 0)
        {
            // Jeżeli jest takich samych znaków to znaczy, że miejsce zerowe
            // jest w [s,x2] zatem nadpisuję lewą granicę środkiem
            // przedziału
            x1 = s;
        } else {
            // Jeżeli nie jest to znaczy, że miejsce zerowe jest w [x1,1]
            // zatem nadpisuję prawą granicę środkiem przedziału
            x2 = s;
        }
        i++;
        printf("  %-10d%-10f%-10f%-10f\n", i, x1, x2, fabs(x1-x2));
    }
    return (x1+x2)/2.0;
}
```
#### Przykład owy od uruchamiający algorytm
```c
int main()
{
    double x1 = 1.0;
    double x2 = 5.0;
    double r = bisection_with_precision(x1,x2 , bsin, 0.01);
    printf("Done: %f\n", r);
    return 0;
}
```
## Doświadczenia
### Wykres funkcji sin(x)
![alt text](https://github.com/finloop/biselect/blob/master/sinx.jpg)
> Wykres funkcji sin(x) wykanany w programie Matlab. 
### Funkcja bsin zwracająca wartość sin(x)
```c
double bsin(double x){
    return sin(x);
}
```
### Funkcja wiel zwracająca wartość wielomianu wiel(x) = 3(x-3)(x-50)(x+50)
```c
double wiel(double x){
    return 3*(x-3)*(x-50)*(x+50);
}
```
### Opis doświadczenia
Algorytm zostanie uruchomiony kilkukrotnie dla funkcji bsin(x) oraz wiel(x). Będziemy badać liczbę iteracji algorytmu zmieniając przedział i precyzję. Następnie porównamy te wyniki z przewidywaniami teoretycznymi.

#### Próba 1
##### Warunki początkowe
- Funkcja bsin(x)
- Przedział [1.0,5.0]
- Precyzja 0.01
- Teoretyczna liczba iteracji: ![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2&space;\frac{|5&space;-&space;1|}{0.01}&space;\approx&space;9).
- Szukane miejsce zerowe w przybliżeniu: 3.14159265359.
##### Wynik uruchomienia
```console
  Iteracja  X1        X2        Obecna precyzja Szukana precyzja
  0         1.000000  5.000000  4.000000        0.010000
  1         3.000000  5.000000  2.000000        0.010000
  2         3.000000  4.000000  1.000000        0.010000
  3         3.000000  3.500000  0.500000        0.010000
  4         3.000000  3.250000  0.250000        0.010000
  5         3.125000  3.250000  0.125000        0.010000
  6         3.125000  3.187500  0.062500        0.010000
  7         3.125000  3.156250  0.031250        0.010000
  8         3.140625  3.156250  0.015625        0.010000
  9         3.140625  3.148438  0.007813        0.010000
Done: 3.144531
```
#### Próba 2
##### Warunki początkowe
- Funkcja bsin(x)
- Przedział [1.0,3.5]
- Precyzja 0.01
- Teoretyczna liczba iteracji: ![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2{\frac{|3,5&space;-&space;1|}{0,01}}&space;\approx&space;8).
- Szukane miejsce zerowe w przybliżeniu: 3.14159265359
##### Wynik uruchomienia
```console
  Iteracja  X1        X2        Obecna precyzja Szukana precyzja
  0         1.000000  3.500000  2.500000        0.010000
  1         2.250000  3.500000  1.250000        0.010000
  2         2.875000  3.500000  0.625000        0.010000
  3         2.875000  3.187500  0.312500        0.010000
  4         3.031250  3.187500  0.156250        0.010000
  5         3.109375  3.187500  0.078125        0.010000
  6         3.109375  3.148438  0.039063        0.010000
  7         3.128906  3.148438  0.019531        0.010000
  8         3.138672  3.148438  0.009766        0.010000
Done: 3.143555
```
#### Próba 3
##### Warunki początkowe
- Funkcja bsin(x)
- Przedział [2.5,5]
- Precyzja 0.01
- Teoretyczna liczba iteracji: ![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2{\frac{|5&space;-&space;2,5|}{0,01}}&space;\approx&space;8).
- Szukane miejsce zerowe w przybliżeniu: 3.14159265359
##### Wynik uruchomienia
```console
  Iteracja  X1        X2        Obecna precyzja Szukana precyzja
  0         2.500000  5.000000  2.500000        0.010000
  1         2.500000  3.750000  1.250000        0.010000
  2         3.125000  3.750000  0.625000        0.010000
  3         3.125000  3.437500  0.312500        0.010000
  4         3.125000  3.281250  0.156250        0.010000
  5         3.125000  3.203125  0.078125        0.010000
  6         3.125000  3.164063  0.039063        0.010000
  7         3.125000  3.144531  0.019531        0.010000
  8         3.134766  3.144531  0.009766        0.010000
Done: 3.139648
```
#### Próba 4
##### Warunki początkowe
- Funkcja wiel(x)
- Przedział [48.5,51.0]
- Precyzja 0.01
- Teoretyczna liczba iteracji: ![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2{\frac{|51&space;-&space;48,5|}{0,01}}&space;\approx&space;8).
- Szukane miejsce zerowe: 50.0
##### Wynik uruchomienia
```console
  Iteracja  X1        X2        Obecna precyzja Szukana precyzja
  0         48.500000 51.000000 2.500000        0.010000
  1         49.750000 51.000000 1.250000        0.010000
  2         49.750000 50.375000 0.625000        0.010000
  3         49.750000 50.062500 0.312500        0.010000
  4         49.906250 50.062500 0.156250        0.010000
  5         49.984375 50.062500 0.078125        0.010000
  6         49.984375 50.023438 0.039063        0.010000
  7         49.984375 50.003906 0.019531        0.010000
  8         49.994141 50.003906 0.009766        0.010000
Done: 49.999023
```
#### Próba 5
##### Warunki początkowe
- Funkcja wiel(x)
- Przedział [48.5,51.0]
- Precyzja 0.0001
- Teoretyczna liczba iteracji: ![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2{\frac{|51&space;-&space;48,5|}{0,0001}}&space;\approx&space;15).
- Szukane miejsce zerowe: 50.0
##### Wynik uruchomienia
```console
  Iteracja  X1        X2        Obecna precyzja Szukana precyzja
  0         48.500000 51.000000 2.500000        0.000100
  1         49.750000 51.000000 1.250000        0.000100
  2         49.750000 50.375000 0.625000        0.000100
  3         49.750000 50.062500 0.312500        0.000100
  4         49.906250 50.062500 0.156250        0.000100
  5         49.984375 50.062500 0.078125        0.000100
  6         49.984375 50.023438 0.039063        0.000100
  7         49.984375 50.003906 0.019531        0.000100
  8         49.994141 50.003906 0.009766        0.000100
  9         49.999023 50.003906 0.004883        0.000100
  10        49.999023 50.001465 0.002441        0.000100
  11        49.999023 50.000244 0.001221        0.000100
  12        49.999634 50.000244 0.000610        0.000100
  13        49.999939 50.000244 0.000305        0.000100
  14        49.999939 50.000092 0.000153        0.000100
  15        49.999939 50.000015 0.000076        0.000100
Done: 49.999977
```
#### Próba 6
##### Warunki początkowe
- Funkcja wiel(x)
- Przedział [48.5,51.0]
- Precyzja od 1 do 10^-9,
- Szukane miejsce zerowe: 50.0
#### Wyniki uruchomienia
```console
 Szukana precyzja    Ilosc iteracji
  1                      2.000000
  0.1                    5.000000
  0.01                   8.000000
  0.001                  12.000000
  0.0001                 15.000000
  1e-005                 18.000000
  1e-006                 22.000000
  1e-007                 25.000000
  1e-008                 28.000000
  1e-009                 32.000000
```
#### Wykres Iteracji od Precyzji(w skali logarytmicznej o podstawie 10)
![alt text](https://github.com/finloop/biselect/blob/master/Iter.png)
### Podsumowanie
- Algorytm za w kazdym przypadku znalazł miejsce zerowe z podaną dokładnością (P1,P2,P3,P4,P5,P6).
- Algorytm działa dla dowolnej funkcji, która w podanym przedziale ma dokładnie 1 miejsce zerowe.
- Zgodnie z przewidywaniami teoretycznymi liczba iteracji nie zależy od funkcji której miejsca zerowego szukamy, co potwierdza próba:P2 i P4.
- Wykres w P6 potwierdza złożoność liniową algorytmu.




