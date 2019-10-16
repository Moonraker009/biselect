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
## Doświadczenie
### Szukanie miejsc zerowych funkcji sin(x)
#### Wykres funkcji sin(x)
![alt text](https://github.com/finloop/biselect/blob/master/sinx.jpg)
> Wykres funkcji sin(x) wykanany w programie Matlab. 
#### Funkcja zwracająca wartość sin(x)
```c
double bsin(double x){
    return sin(x);
}
```
#### Opis doświadczenia
Algorytm zostanie uruchomiony dla przedziału [1,5], z precyzją P=0.01 dla funkcji sin(x). Zgodnie z przewidywaniami teoretycznymi liczba iteracji powinna wynosić: ![](https://latex.codecogs.com/gif.latex?n&space;=&space;\log_2&space;\frac{|5&space;-&space;1|}{0.01}&space;\approx&space;9).
#### Kod
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
#### Wynik
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
#### Wykresy

#### Wnioski
Jak widać algorytm zadziałał prawidłowo, uzyskał odpowiednią precyzję, a ilość jego przejść po pętli zgaza się z wyliczeniami teoretycznymi. 




