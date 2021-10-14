---
layout: single
title:  "wykop.pl - Analiza znalezisk"
date:   2021-10-12 14:16:43 +0200
categories: jekyll update
---

<img src="{{site.url}}/assets/images/posts/wykoptags/logo_wykop_500.png" style="display: block; margin: auto;" />

Analiza najpopularniejszych znalezisk z ostatnich lat.

## Pobranie Danych

Dane do analizy zostały pobrane przy wykorzystaniu skyptu *./bin/download_hits.sh*, o którym była mowa w tekscie poświęconym wyciąganiu danych poprzez REST API. Skrypt ten dostępny jest w repozytorium *GitHub*. Jego zadaniem jest pobrane najpopularniejszych znalezisk z danego miesiąca. Aby pobrać znaleziska z wielu miesięcy możemy wykorzytać prostą pętlę napisaną w języku powłoki *bash*.

```bash
for year in `seq 2006 2021`
do
    for month in `seq 1 12`
    do
        ./bin/download_hits.py $year $month notebooks/links $WYKOP_API_KEY
    done
done
```

Ostatecznie pobrano 339 033 znalezisk.

## Wyniki Analizy

Notebook z kodem do analizy dostepny jest w repozytorium GitHub: notebooks/links-analysis.ipynb.

#### Minimalna liczba wykopów potrzebna, aby znaleźć się w top najlepszych znalezisk

Rysunek 1 zawiera cztery wykresy przedstawiającę minimalną liczbę wykopów potrzebną aby znaleźć się w top *n* najlepszych znalezisk w danym miesiącu. W przypadku wszystkich wykresów możemy zaobserwować wyraźną tendencę wzrostową. **W 2020-2021 znaleziska musiało uzbierać średnio ok. 3500 wykopów aby trafić do *top 50*** (dolny prawy wykres). Dzięki temu mogło trafić na na pierwszą stronę hitów miesiąca. W porównaniu do poprzednich lat, próg ten wzrósł o ok. 1000. W latach 2015-2019 aby to osiągnąć, wystarczyło 2500 wykopów. **Próg wejścia do *top 5* w latach 2020-2021 oscylował w granicach 6000 wykopów**, a tym samym zwiększył się w ostatnich lat o ok. 2000. Wygląda na to, że coraz trudniej trafić do top znalezisk danego miesiąca.

<figure>
    <figcaption>Rysunek 1. Minimalna liczba wykopów, aby znaleźć się w top najlepszych znalezisk.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/min_votes.png" style="display: block; margin: auto;" />
</figure>

#### Najpopularniejsze tagi

Każde znaleziska opatrzone jest listą tagów, które opisują mniej więcej tematykę znaleziska. Na tej podstawie możemy okreslić jakie tematy były najpopularniejsze w danym okresie oraz jak zmieniały się zainteresowania użytkowników serwisu na przestrzeni lat.

W pierwszej kolejności porównamy najpopularniejsze tagi dla dwóch skrajnych lat, 2008 oraz 2021. Na rysunku 2 przedstawiono wykres słupkowy zawierający informację o tym jaki procent wszystkich najpopularniejszych znalezisk z danego roku posiadało dany tag. Wykorzystano 10 najpopularniejszych tagów z obydwu lat.

Już na pierwszy rzut oka widać wyraźne różnicę pomiędzy obydwoma latami. Przede wszystkim pojawiło się wiele nowych tagów, które w 2008 wogóle nie występowały. Jednym z nich jest *#koronawirus*, co wydaje się uzasadnione. Inne nowe tagi to *#bekazpisu* oraz *#neuropa*. Dodatkowo kilka tagów zyskało dużo wiekszą popularność jak np. *#polska* (wzrost z 20.37% do 53.11%), *#polityka* (wzrost z 2.2% do 14.22%) oraz *#ciekawostki* (wzrost z 9.51% do 18.79%). Z drugiej strony, w 2021 tag *#nowetechnologie* w ogóle nie występował, chociaż w 2008 ok. 11% wszystkich znalezisk go posiadało. Duży spadek zaliczyły takie tagi jak *#humor* (spadek z 32.47% do 0.66%), *#internet* (spadek z 6.71% do 1.03%) oraz *#matematyka* (spadek z 6.28% do 0.1%). W 2008 jednymi z popularniejszych znalezisk były te, które dotyczyły matematyki! W przypadku niektórych tagów różnica pomiędzy analizowanymi latami jest nieznaczna, jak np. *#nauka* (7.49% w 2008 oraz 6.24% w 2021) lub *#swiat* (15.88% w 2008 oraz 16.87% w 2021).

<figure>
    <figcaption>Rysunek 2. Udział znalezisk opatrzonych wybranymi tagami w 2008 oraz 2021.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular_tags_shift.png" style="display: block; margin: auto;" />
</figure>

**Mamy do czynienia z wyraźnym przesunięciem tematyki najpopularniejszych znalezisk z obszarów związanych z humorem, nowymi technologiami i Internetem do obszarów dotyczących polityki oraz ogólnie wydarzeń mających miejsce w naszym kraju**. W dużej mierze związane jest to zapewne ze zmianą profilu typowego użytkownika serwisu. Możemy postawić hipotezę, że w 2008 był to prawdopodobnie dwudziestoparoletni mężczyzna zainteresowany nowymi technologiami. Z kolei w 2021 jest to raczej trzydziesto-czterdziestoletni mężczyzna lub kobieta, interesujacy się polityką oraz bieżącymi wydareniami. Czyżby użytkownicy *wykop.pl* zwyczajnie dorośli?

Tabela 1 zawiera podobne informację jak rysunek 2, z tą rożnicą, że uwzględniono wszystkie analizowane lata 2006-2021. Dzięki temu, możemy  dokładniej przeanalizować jak zmieniały się zainteresowania użytkowników w czasie. 

<figure>
    <figcaption>Tabela 1. Udział znalezisk opatrzonych wybranymi tagami w latach 2006-2021.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular-tags-table.png" style="display: block; margin: auto;" />
</figure>

Poniżej kilka spostrzeń:

- W 2010 miała miejsce istotna zmiana. Udział znaleziska z tagiem *#humor# spadł z 27.5% w 2009 do 9.4% w 2010. Podobna sytuacja miała miejsce w przypadku tagów *#nowetechnologie* oraz *#rozrywka*. Z kolei dużą popularność zyskały znaleziska z tagami *#polityka* oraz *#zainteresowania*.
- Tematyka najpopularniejszych znalezisk w 2020-2021 wydaje się być bardziej różnorodna niż na początku badanego okresu. W 2008-2010 dominowały tematy związne z technologią, humorem oraz rozrywką. W 2021 wykluczając tag *#polska*, który jest bardzo ogólny, nie ma już jednej wyraźnie dominującej tematyki. Zainteresowania użytkowników wydają się być bardziej równomiernie rozłożone.
- Znaleziska z tagiem *#ekonomia* stają się coraz bardziej popularne. Ich udział w ostatnich latach systematycznie rośnie.
- W 2020 *wykop.pl* żył *#koronowirus#. W 2021 tag ten jest jeszcze popularny, ale już nie tak bardzo jak w roku poprzednim.
- W 2015 i 2016 bardzo popularne były znaleziska związane z polityką, co wynikało zapewne z wyborów, które miały miejsce w 2015. Udział tych znalezisk ocylował w granicach 26%.
- Znaleziska z tagiem *#neuropa* zyskują na popularności. W 2021 odpowiadały już za 8.8% wszystkich najpopularniejszych znalezisk.

Na rysunku 3 zobrazowano zmiany udziału znalezisk z danymi tagami wykorzystując wykres liniowy.

<figure>
    <figcaption>Rysunek 3. Udział znalezisk opatrzonych wybranymi tagami w latach 2006-2021.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular_tags_series.png" style="display: block; margin: auto;" />
</figure>

Na zakońcnie tej sekcji, sporządzimy listę tagów, które miały swoje 5 minut. Wskazują one na jednorazowe, istotne wydarzenia, które poruszyły *wykop.pl*. I tak np. na początku 2012 wyjątkowo popularne były znaleziska dotyczące ustawy ACTA. W maju 2013 miała miejsce [afera zbożowa](https://wykopedia.fandom.com/pl/wiki/Afera_zbo%C5%BCowa). W 2015-09 mieliśmy do czynienia z najazdem imigrantów na Europę. Użytkownicy serwisu zapewne byliby w stanie dokładnie wskazać wydarzenia, które miały miejsce w pozostałych miesiącach.

<figure>
    <figcaption>Tabela 2. Krótkotrwale popularne tagi.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/tags-events.png" style="display: block; margin: auto;" />
</figure>