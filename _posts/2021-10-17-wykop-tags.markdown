---
layout: single
title:  "wykop.pl - Analiza znalezisk"
date:   2021-10-17 00:00:00 +0200
categories: data-science
excerpt: "**wykop.pl** jest jednym z popularniejszych serwisów społecznościowych w Polsce. W tekście przedstawiamy wyniki analizy treści publikowanych przez użytowników."
---

<img src="{{site.url}}/assets/images/posts/wykoptags/logo_wykop_500.png" style="display: block; margin: auto;" />

**wykop.pl**[^1] jest jednym z popularniejszych serwisów społecznościowych w Polsce ([źródło](https://www.wirtualnemedia.pl/artykul/tiktok-ma-w-polsce-wiecej-uzytkownikow-niz-twitter-pinterest-przed-snapchatem-i-wykopem-top10)). Idea serwisu opiera się na tzw. znaleziskach, czyli informacjach, które są dodawane, oceniane i komentowane przez użytkowników. Znaleziska z największą liczbą dodatnich głosów (tzw. wykopów) trafiają na stronę główną. *wykop.pl* udostępnia swoje dane poprzez publiczne REST API, o czym pisaliśmy w jednym z poprzednich [tekstów](). Wykorzystując zebrane dane, postanowiliśmy przeanalizować najpopularniejsze znalezisk z ostatnich lat. W tekście przedstawiamy wyniki naszej analizy.

## Dane

Dane do analizy zostały pobrane przy wykorzystaniu skyptu *./bin/download_hits.sh*, o którym była mowa w tekście poświęconym wyciąganiu danych poprzez REST API. Skrypt ten dostępny jest w [repozytorium GitHub](https://github.com/dsinaction/wykop-tags). Jego zadaniem jest pobrane najpopularniejszych znalezisk z danego miesiąca. Aby pobrać znaleziska z wielu miesięcy, możemy wykorzystać prostą pętlę napisaną w języku powłoki *bash*.

```bash
for year in `seq 2006 2021`
do
    for month in `seq 1 12`
    do
        ./bin/download_hits.py $year $month notebooks/links $WYKOP_API_KEY
    done
done
```

Do analizy pobrano 339 033 najpopularniejszych znalezisk. Należy mieć na uwadze, że jest to zapewne jedynie niewielka część wszystkich znalezisk, jakie kiedykolwiek zostały dodane przez użytkowników serwisu.

## Wyniki Analizy

Notebook z kodem wykorzystanym do analizy dostępny jest w repozytorium [GitHub](https://github.com/dsinaction/wykop-tags): *notebooks/links-analysis.ipynb*.

#### Minimalna liczba wykopów potrzebna, aby znaleźć się w top najlepszych znalezisk

Rysunek 1 zawiera cztery wykresy przedstawiające minimalną liczbę wykopów potrzebną, aby znaleźć się w top *n* najlepszych znalezisk w danym miesiącu. W przypadku wszystkich wykresów możemy zaobserwować wyraźną tendencje wzrostową. **W 2020-2021 znaleziska musiało uzbierać średnio ok. 3500 wykopów, aby trafić do *top 50*** (dolny prawy wykres). Dzięki temu mogło trafić na pierwszą stronę hitów miesiąca. W porównaniu do poprzednich lat próg ten wzrósł o ok. 1000. W latach 2015-2019, aby to osiągnąć, wystarczyło 2500 wykopów. **Próg wejścia do *top 5* w latach 2020-2021 oscylował w granicach 6000 wykopów**, a tym samym zwiększył się w ostatnich lat o ok. 2000. Wygląda na to, że coraz trudniej trafić do top znalezisk danego miesiąca. Z drugiej strony mieliśmy zapewne do czynienia z rosnącą liczbą użytkowników na przestrzeni badanego okresu, przez co zwiększeniu uległa również pula potencjalnych osób, które mogły wykopać dane znalezisko.

<figure>
    <figcaption>Rysunek 1. Minimalna liczba wykopów, aby znaleźć się w top najlepszych znalezisk.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/min_votes.png" style="display: block; margin: auto;" />
</figure>

#### Najpopularniejsze tagi

Każde znaleziska opatrzone jest listą tagów, które opisują mniej więcej tematykę znaleziska. Na tej podstawie możemy określić jakie tematy były najpopularniejsze w danym okresie oraz jak zmieniały się zainteresowania użytkowników serwisu na przestrzeni lat.

W pierwszej kolejności porównamy najpopularniejsze tagi dla dwóch skrajnych lat, 2008 oraz 2021. Na rysunku 2 przedstawiono wykres słupkowy zawierający informację o tym, jaki procent wszystkich najpopularniejszych znalezisk z danego roku posiadało dany tag. Wykorzystano 10 najpopularniejszych tagów z obydwu lat.

<figure>
    <figcaption>Rysunek 2. Udział znalezisk opatrzonych wybranymi tagami w 2008 oraz 2021.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular_tags_shift.png" style="display: block; margin: auto;" />
</figure>

Już na pierwszy rzut oka widać wyraźną różnicę pomiędzy obydwoma latami. Przede wszystkim pojawiło się wiele nowych tagów, które w 2008 w ogóle nie występowały. Jednym z nich jest *#koronawirus*, co wydaje się uzasadnione. Inne nowe tagi to *#bekazpisu* oraz *#neuropa*. Dodatkowo kilka tagów zyskało dużo większa popularność jak np. *#polska* (wzrost z 20.37% do 53.11%), *#polityka* (wzrost z 2.2% do 14.22%) oraz *#ciekawostki* (wzrost z 9.51% do 18.79%). Z drugiej strony, w 2021 tag *#nowetechnologie* w ogóle nie występował, chociaż w 2008 ok. 11% wszystkich znalezisk go posiadało. Duży spadek zaliczyły takie tagi jak *#humor* (spadek z 32.47% do 0.66%), *#internet* (spadek z 6.71% do 1.03%) oraz *#matematyka* (spadek z 6.28% do 0.1%). W 2008 jednymi z popularniejszych znalezisk były te, które dotyczyły matematyki! W przypadku niektórych tagów różnica pomiędzy analizowanymi latami jest nieznaczna jak np. *#nauka* (7.49% w 2008 oraz 6.24% w 2021) lub *#swiat* (15.88% w 2008 oraz 16.87% w 2021).

**Mamy do czynienia z wyraźnym przesunięciem tematyki najpopularniejszych znalezisk z obszarów związanych z humorem, nowymi technologiami i Internetem do obszarów dotyczących polityki oraz ogólnie wydarzeń mających miejsce w naszym kraju**. W dużej mierze związane jest to zapewne ze zmianą profilu typowego użytkownika serwisu. Możemy postawić hipotezę, że w 2008 był to prawdopodobnie dwudziestoparoletni mężczyzna zainteresowany nowymi technologiami. Z kolei w 2021 jest to raczej trzydziesto-czterdziestoletni mężczyzna lub kobieta, interesujący/a się polityką oraz bieżącymi wydarzeniami. Czyżby użytkownicy *wykop.pl* zwyczajnie dorośli?

Tabela 1 zawiera zbliżoną informację jak rysunek 2, z tą różnicą, że uwzględniono wszystkie analizowane lata 2006-2021. Dzięki temu możemy dokładniej przeanalizować, jak zmieniały się zainteresowania użytkowników w czasie.

<figure>
    <figcaption>Tabela 1. Udział znalezisk opatrzonych wybranymi tagami w latach 2006-2021.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular-tags-table.png" style="display: block; margin: auto;" />
</figure>

Poniżej kilka spostrzeżeń:

- W 2010 miała miejsce istotna zmiana. Udział znalezisk z tagiem *#humor#* spadł z 27.5% w 2009 do 9.4% w 2010. Podobna sytuacja miała miejsce w przypadku tagów *#nowetechnologie* oraz *#rozrywka*. Z kolei dużą popularność zyskały znaleziska z tagami *#polityka* oraz *#zainteresowania*.
- Tematyka najpopularniejszych znalezisk w 2020-2021 wydaje się bardziej różnorodna niż na początku badanego okresu. W 2008-2010 dominowały tematy związane z technologią, humorem oraz rozrywką. W 2021 wykluczając tag *#polska*, który jest bardzo ogólny, nie ma już jednej wyraźnie dominującej tematyki. Zainteresowania użytkowników wydają się bardziej równomiernie rozłożone.
- Znaleziska z tagiem *#ekonomia* stają się coraz bardziej popularne. Ich udział w ostatnich latach systematycznie rośnie.
- W 2020 *wykop.pl* żył *#koronowirus#. W 2021 tag ten jest jeszcze popularny, ale już nie tak bardzo, jak w roku poprzednim.
- W 2015 i 2016 bardzo popularne były znaleziska związane z polityką, co wynikało zapewne z wyborów, które miały miejsce w 2015. Udział tych znalezisk oscylował w granicach 26%.
- Znaleziska z tagiem *#neuropa* zyskują na popularności. W 2021 odpowiadały już za 8.8% wszystkich najpopularniejszych znalezisk.

Na rysunku 3 zobrazowano zmiany udziału znalezisk z danymi tagami przy wykorzystaniu  wykresu liniowego.

<figure>
    <figcaption>Rysunek 3. Udział znalezisk opatrzonych wybranymi tagami w latach 2006-2021.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular_tags_series.png" style="display: block; margin: auto;" />
</figure>

Na zakończenie tej sekcji, sporządzimy listę tagów, które miały swoje 5 minut. Wskazują one na jednorazowe, istotne wydarzenia, które poruszyły *wykop.pl*. I tak np. na początku 2012 wyjątkowo popularne były znaleziska dotyczące ustawy ACTA. W maju 2013 miała miejsce [afera zbożowa](https://wykopedia.fandom.com/pl/wiki/Afera_zbo%C5%BCowa). W 2015-09 mieliśmy do czynienia z najazdem imigrantów na Europę. Użytkownicy serwisu zapewne byliby w stanie dokładnie wskazać wydarzenia, które miały miejsce w pozostałych miesiącach.

<figure>
    <figcaption>Tabela 2. Krótkotrwale popularne tagi.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/tags-events.png" style="display: block; margin: auto;" />
</figure>

#### Najpopularniejsze źródła

Każde znalezisko posiada swoje źródło. Tabela 3 zawiera dane na temat udziału znalezisk z najpopularniejszych źródeł w latach 2006-2021.

<figure>
    <figcaption>Tabela 3. Udział znalezisk z danego źródła w latach 2006-2021 (z wyłączniem *wykop.pl* oraz *youtube.com*).</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular-sources.png" style="display: block; margin: auto;" />
</figure>

**Najpopularniejszymi źródłami są bezsprzecznie *wykop.pl* (treści przygotowane przez użytkowników) oraz *youtube.com***. To pierwsze odpowiadało za ok. 19% najpopularniejszych znalezisk w latach 2020-2021. Natomiast *youtube.com* za ok. 15.5%. Trzecie miejsce przypadło platformie *stremable.com*, której udział dynamiczne wzrósł w ostatnich latach. Popularny jest również *twitter.com* z udziałem ok. 2.5%. Pozostałe źródła nie przekraczają 2% udziału. Spośród nich popularne są przede wszystkim serwisy informacyjne jak np. *tvn24.pl*, *onet.pl*, *money.pl* lub *polsatnews.pl*. Jeżeli chodzi o źródła, które utraciły swoje znaczenie, to mamy tutaj przede wszystkim *pokazywarka.pl*, która w latach 2009-2011 odpowiadała za ok. 4.5% najpopularniejszych znalezisk. Podobnie źródło *liveleak.com*, które w swoich szczytowych latach 2013-2014 obejmowało ok. 2.5% znalezisk.

#### Użytkownicy z największą liczbą popularnych znalezisk

W kolejnym etapie analizy poszukamy użytkowników z największą liczbą popularnych znalezisk. Ograniczmy popularne znaleziska do tych, które uzyskały co najmniej 100 wykopów. W tabeli 4 przestawiono 3 najlepszych użytkowników dla poszczególnych lat.

<figcaption>Tabela 4. Ranking użytkowników z największą liczbą popularnych znalezisk.</figcaption>

| Rok      | Pierwsze Miejsce | Drugie Miejsce     | Trzecie Miejsce |
| :----:       |     ---:  |          ---: |          ---: |
| <sub>**2006**</sub> | <sub>KKKas - 7 (2.8%)</sub> | <sub>gordi7 - 7 (2.8%)</sub> | <sub>adas - 5 (2.0%)</sub> | 
| <sub>**2007**</sub> | <sub>NamalowanyPr - 38 (2.3%)</sub> | <sub>rybeczka - 23 (1.4%)</sub> | <sub>NowyPremier - 13 (0.8%)</sub> | 
| <sub>**2008**</sub> | <sub>rybeczka - 68 (1.3%)</sub> | <sub>_tomek_ - 57 (1.1%)</sub> | <sub>tomaszs - 39 (0.8%)</sub> | 
| <sub>**2009**</sub> | <sub>_tomek_ - 144 (1.5%)</sub> | <sub>kaszpirofsky - 118 (1.3%)</sub> | <sub>NiEb0 - 108 (1.1%)</sub> | 
| <sub>**2010**</sub> | <sub>reddigg - 106 (1.1%)</sub> | <sub>kaszpirofsky - 87 (0.9%)</sub> | <sub>kubatre1 - 84 (0.8%)</sub> | 
| <sub>**2011**</sub> | <sub>reddigg - 453 (2.5%)</sub> | <sub>kubatre1 - 236 (1.3%)</sub> | <sub>abram66 - 148 (0.8%)</sub> | 
| <sub>**2012**</sub> | <sub>kajakkajak - 217 (1.1%)</sub> | <sub>GraveDigger - 185 (1.0%)</sub> | <sub>siwymaka - 174 (0.9%)</sub> | 
| <sub>**2013**</sub> | <sub>p........... - 220 (0.9%)</sub> | <sub>c....o - 206 (0.9%)</sub> | <sub>GraveDigger - 205 (0.9%)</sub> | 
| <sub>**2014**</sub> | <sub>darosoldier - 1125 (4.6%)</sub> | <sub>sportingkiel - 739 (3.0%)</sub> | <sub>reflex1 - 361 (1.5%)</sub> | 
| <sub>**2015**</sub> | <sub>darosoldier - 1044 (3.3%)</sub> | <sub>n..2 - 898 (2.8%)</sub> | <sub>sportingkiel - 723 (2.3%)</sub> | 
| <sub>**2016**</sub> | <sub>WuDwaKa - 814 (2.9%)</sub> | <sub>i.....n - 641 (2.3%)</sub> | <sub>darosoldier - 479 (1.7%)</sub> | 
| <sub>**2017**</sub> | <sub>Mesk - 1091 (3.6%)</sub> | <sub>HaHard - 806 (2.6%)</sub> | <sub>a..........a - 777 (2.5%)</sub> | 
| <sub>**2018**</sub> | <sub>a..........a - 706 (2.3%)</sub> | <sub>Cziken1986 - 699 (2.3%)</sub> | <sub>darosoldier - 528 (1.7%)</sub> | 
| <sub>**2019**</sub> | <sub>Szewczenko - 686 (2.3%)</sub> | <sub>HaHard - 670 (2.3%)</sub> | <sub>regiony - 401 (1.4%)</sub> | 
| <sub>**2020**</sub> | <sub>Szewczenko - 1990 (4.7%)</sub> | <sub>HaHard - 641 (1.5%)</sub> | <sub>tomasztomasz - 590 (1.4%)</sub> | 
| <sub>**2021**</sub> | <sub>Szewczenko - 1812 (6.2%)</sub> | <sub>Oline - 548 (1.9%)</sub> | <sub>gromota - 415 (1.4%)</sub> | 

W ostatnich latach zdecydowanym liderem pod względem popularnych znalezisk jest **Szewczenko**, który np. w 2020 dodał 1990 znalezisk, które uzyskały co najmniej 100 pozytywnych głosów. Znaleziska dodane przez tego użytkownika 4.7% wszystkich takich znalezisk. W obecnie trwającym roku również jest on liderem i to z nawet większym udziałem - 6.2%. Kolejny użytkownik na liście - **Oline** odpowiada za 1.9% popularnych znalezisk.

Analizując pierwsze miejsca, możemy zaobserwować, że w przeszłości trzykrotnie wystąpiła sytuacja, w której jeden użytkownik dwa lata z rzędu był liderem rankingu. W latach 2010-2011 był to **reddigg**, w 2014-2015 **darosoldier**. Obecnie jest to **Szewczenko**, który zajmuje pierwsze miejsce nieprzerwanie od 2019 i prawdopodobnie jako pierwszy utrzyma tytuł lidera nieprzerwanie przez trzy lata z rzędu.

Na zakończenie tej części porównamy jeszcze tematykę znalezisk trzech najczęściej wykopywanych użytkowników z 2021: *Szewczenko*, *Oline*, *gromata*. Na rysunku 4 przedstawiono wykres zawierający informację o tym, jaki procent wszystkich znalezisk danego autora z 2021 posiadało dany tag.

<figure>
    <figcaption>Rysunek 4. Udział znalezisk danego użytkownika z danym tagiem w 2021 w jego wszystkich popularnych znaleziskach.</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/popular_author_tags.jpeg" style="display: block; margin: auto;" />
</figure>

W przypadku *Szewczenko* większość jego znalezisk posiadała tag *#polska* - 83.77%. Duży udział stanowiły również znaleziska z tagiem *#bekazpisu* (52.98%), *#wydarzenia* (40.78%), *#prawo* (29.58%). Drugi na liście *Oline* również dodał sporo znalezisk z tagiem *#polska* (58.39%). Udział pozostałych tagów tego użytkownika, jak np. *#pieniadze*, *#gospodarka*, *#finanse* oraz *#pis*, kształtuje się na zbliżonym poziomie ok. 14%. Z kolei *gromoto* dodał bardzo dużo znalezisk z tagiem *#neuropa* - 89.64%, czym zdecydowanie wyróżnia się na tle pozostałych dwóch użytkowników. Sporo jego znalezisk miało również przypisany tag *#bekazpisu* (68.92%) oraz *#tvpis* (40.72%). Podsumowując, możemy powiedzieć, że ***Szewczenko* oraz *gromoto* specjalizują się w tematach związanych z aktualnymi wydarzeniami oraz polityką, *Oline* z kolei w tematach związanych z ekonomią, gospodarką oraz finansami.**

#### Popularne słowa w tytule

Techniką, która pozwala w graficzny sposób zobrazować popularne i często występujące słowa, jest tzw. chmura wyrazów (ang. word cloud). Powstały obraz składa się z popularnych wyrazów, których wielkość jest zależna od częstości ich wystąpień. Rysunki 5 oraz 6 przedstawiają chmury wyrazów dla lat 2008 i 2021.

<figure>
    <figcaption>Rysunek 5. Popularne słowa w tytułach znaleziska z 2008 (chmura słów).</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/wordcloud_2008.png" style="display: block; margin: auto;" />
</figure>


<figure>
    <figcaption>Rysunek 6. Popularne słowa w tytułach znaleziska z 2021 (chmura słów).</figcaption>
    <img src="{{site.url}}/assets/images/posts/wykoptags/wordcloud_2021.png" style="display: block; margin: auto;" />
</figure>

**Słowami, które rzucają się w oczy dla roku 2008 są: *google*, *pic*, *polska*, *świecie*, *wykop*, *reklama*. Z kolei dla 2021 są to *polska*, *policja*, *usa*, *rząd*, *covid*, *tvp*.**

### Podsumowanie

Analiza najpopularniejszych znalezisk z serwisu *wykop.pl* pozwoliła uzyskać wgląd w zainteresowania użytkowników serwisu oraz tego, jak zmieniały się on na przestrzeni ostatnich kilkunastu lat. Stwierdzono wyraźne przesunięcie tematyki znalezisk z obszaru technologi oraz rozrywki, do tematów dotyczących polityki oraz aktualnych wydarzeń. Popularne okazały się również treści tworzone przez samych użytkowników. Na drugim miejscu znalazł się *youtube.com*. W miarę wzrostu popularności serwisu uplasowanie znaleziska na głównej stronie wymagało zdobycia coraz większej liczby wykopów. Jednak nie przeszkodziło to kilku użytkownikom, na coroczne zajmowanie wysokich pozycji w rankingu użytkowników z największą liczbą popularnych znalezisk.

[^1]: Polski serwis internetowy z kategorii tzw. social news, istniejący od 28 grudnia 2005. W założeniu jest on odpowiednikiem amerykańskiego Reddita oraz digg.com. Ideą wykop.pl jest odnajdywanie interesujących informacji w Internecie i przedstawianie ich innym użytkownikom (źródło: [Wikipedia](https://pl.wikipedia.org/wiki/Wykop.pl)).