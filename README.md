

Skupiono się na przeprowadzeniu serii testów oprogramowania Snort za pomocą wygenerowanego ruchu sieciowego szkodliwego oraz ruchu nieszkodliwego w celu zbadania skuteczności oprogramowania Snort . W tym celu przystąpiono do zbudowania architektury środowiska testowego. W tym celu wykorzystano oprogramowanie  środowiska wirtualnego Oracle Virtual Box . Utworzono trzy maszyny wirtualne. Na pierwszej z nich zainstalowano system operacyjny Kali Linux, zawierający pakiet Metasploit.  Maszyna ta została wykorzystana jako potencjalne urządzenie atakujące. Na kolejnej maszynie wirtualnej zainstalowano system operacyjny Linux Ubuntu z oprogramowaniem Snort pełniącym funkcję maszyny atakowanej.   Na trzecim urządzeniu wirtualnym z systemem Linux Ubuntu zainstalowano oprogramowanie Ostinato . Ostatnia maszyna i zainstalowany na niej pakiet pełnią funkcję generatora ruchu neutralnego. Całość oprogramowania umieszczono w wirtualnej sieci odseparowanej od sieci rzeczywistej.



Pełniona funkcja urządzenia |Cechy urządzenia|
|--|--|
| Generator złośliwego ruchu | Maszyna wirtualna, Kali Linux, 4 CPU 2,2 GHz, 4GB RAM, 10 Gbps Ethernet, Metasploit |
| Generator neutralnego ruchu |Maszyna wirtualna, Ubuntu, 4 CPU 2,2 GHz, 4GB RAM, 10 Gbps Ethernet, Ostinato  |
| Urządzenie atakowane |Maszyna wirtualna, Ubuntu, 4 CPU 2,2 GHz, 4GB RAM, 10 Gbps Ethernet, Snort 3.1.77.0 |
| Wirtualny przełącznik| |



W pierwszej fazie testów przystąpiono do analizy obciążenia środowiska sieciowego w zależności od generowanego natężenia ruchu sieciowego. Kolejne pakiety były implementowane  jako ruch w tle o szybkości sieci od 1 Gb/s do 10 Gb/s w następujący sposób.

- 1 000 000 pakietów UDP z szybkością 500 pakietów/s, każdy pakiet o rozmiarze 1470 bajtów  
- 1 000 000 pakietów TCP z szybkością 500 pakietów/s, każdy pakiet ma rozmiar 1470 bajtów
- 1 000 000 pakietów ICMP z szybkością 1000 pakietów/s, każdy pakiet ma rozmiar 1470 bajtów.


W kolejnej fazie testów weryfikowano, czy wybrany zestaw reguł Snort jest w stanie właściwie zakwalifikować szkodliwy oraz nieszkodliwy ruch sieciowy. W tym teście pominięto fałszywie pozytywne alerty jako mało istotne w przypadku analizy skuteczności wykrywania szkodliwego ruchu sieciowego.

Odsetek wyników fałszywie dodatnich (FPR) - Jest to prawdopodobieństwo, że IDS uruchomi alarm, gdy nie dojdzie do włamania. Mierzony za pomocą zawartości procentowej FPR w całości próbek. Wyzwala nadmierną liczbę fałszywych alarmów, gdy IDS niedokładnie rozróżnia ruch szkodliwy i nieszkodliwy.

Odsetek wyników fałszywie ujemnych (FNR) - jest to prawdopodobieństwo, że IDS nie uruchomi alarmu w przypadku włamania. Mierzone według wartości procentowej FNR w całkowitej liczbie przypadków. Nie wyzwala fałszywych alarmów ujemnych i przepuszcza ruch sieciowy, gdy IDS nie ma ustawionej odpowiedniej reguły dopasowania dla ruchu sieciowego.

Wskaźnik prawdziwie dodatni (TPR) - jest to prawdopodobieństwo, że system IDS wyzwoli alarm po wykryciu włamania. Zostało to zmierzone za pomocą zawartości procentowej TPR w całości próbek. Wyzwala prawdziwie pozytywny alarm, gdy dokładnie wykryje złośliwy ruch.

Testy w dalszym kroku podzielono na dwie części:  
- pomiar wskaźników wyników fałszywie dodatnich, fałszywie ujemnych i prawdziwie dodatnich  IDS z ruchem neutralnym
- pomiar wskaźników wyników fałszywie dodatnich, fałszywie ujemnych i prawdziwie dodatnich IDS z ruchem łączonym ruch złośliwy ruch neutralny ze stałą prędkością sieci 10 Gb/s.

Użyto domyślnego zestawu reguł i domyślnej konfiguracji wydajności systemu Snort. W celu badania wykorzystano 7 różnych typów protokołów, z którymi związane były ataki. 

| Rodzaj ruchu/protokołu  |Liczba wykorzystanych reguł |
|--|--|
| ICMP|130  |
|ARP  |22  |
|SCAN|30  |
|SSH |15  |
|DoS/DDoS  |70  |
|FTP  |80 |
|HTTP  |100 |

Eksperyment wymagał serwera docelowego wspierającego usługi HTTP, FTP i SSH. Za pomocą Kali Linux Metasploit  wygenerowano siedem rodzajów szkodliwego ruchu wraz z legalnym ruchem. Wszystkie pakiety zostały wprowadzone do IDS w celu symulacji ataków. Celem IDS było sprawdzenie, czy legalny i złośliwy ruch  uruchomi określone alarmy, gdy ruch wejściowy będzie zgodny z zestawem reguł. Liczba alarmów (fałszywie dodatnich, fałszywie ujemnych i prawdziwie dodatnich) pokaże, jak dokładnie Snort klasyfikuje ruch sieciowy. Do generowania złośliwego oprogramowania wykorzystywany jest framework Metasploit ruch z różnymi exploitami i payloadami. Podstawowym zadaniem przed wykonaniem ataku jest przeskanowanie otwartych portów atakowanego urządzenia w celu analizy możliwości wykorzystania określonych protokołów. 

W celu uruchomienia exploita potrzebne są informacje o systemie będącym celem ataku, takie jak system operacyjny i uruchomione przez niego usługi. Informacje te można zebrać za pomocą narzędzi do skanowania, takich jak Nmap lub innych podobnych narzędzi. Framework Metasploit ma charakter modułowy i możemy mieszać i dopasowywać różne ładunki. 

W kolejnym teście analizowano dokładność wykrywania Snort podczas przetwarzania neutralnego  i złośliwego ruchu sieciowego. IDS został ustawiony w sposób domyślny. Pierwszy test został wykonany przy użyciu generatora neutralnego ruchu sieciowego, który wprowadzał pakiety UDP, TCP i ICMP.

W kolejnej fazie testów dokonano analizy zachowania systemu w przypadku wprowadzenia zarówno ruchu neutralnego jak i ruchu złośliwego. Test podzielono na trzy 4-godzinne tury. W każdej turze dokonano określonej liczby ataków związanych z popularnymi protokołami: SSH, DDoS, FTP, HTTP, ICMP, SCAN, ARP.

Siedem różnych rodzajów szkodliwego ruchu zostało wprowadzonych do Snort. Największe średnie FPR odnotowano dla pakietów typu ICMP, FTP oraz SSH. Można wnioskować po tym, że system w podstawowej konfiguracji jest zbyt czuły w przypadku wyżej wymienionych pakietów, co może skutkować zablokowaniem znacznej części nieszkodliwych pakietów. Dla odmiany w przypadku pakietów ICMP, FTP oraz SSH odsetek FNR okazał się najniższy. Rozrzut wyników od średniej okazał się najmniejszy w przypadku FPR dla ataków typu Scan oraz HTTP. W przypadku FNR, zaś dla ataków typu SSH. Świadczy to o dużej powtarzalności działania oraz skuteczności reguł dla trzech prób badawczych. Problemem Snorta jest jego wysoki wskaźnik alertów fałszywie pozytywnych. Rozwiązanie tego problemu jest niezwykle istotne, ponieważ analiza fałszywych alarmów pochłania czas administratora i zasoby obliczeniowe. Kolejnym celem było więc zmniejszenie FPR w Snort za pomocą technik sztucznej inteligencji.

W kolejnej części rozważaniu poddano oprogramowanie Snort z wdrożonym modułem sztucznej inteligencji. Snort w standardzie bazuje na bazie reguł wykrywających złośliwy ruch. Aby zmniejszyć FPR, wielu badaczy wykorzystało algorytmy uczenia maszynowego do klasyfikowania normalnego i złośliwego ruchu. Następujące algorytmy uczenia maszynowego zostały szeroko zbadane, takie jak  Support Vector Machine, Decision Trees, Fuzzy Logic, BayesNet oraz NaiveBayes.

Testy przeprowadzono podobnie jak w  poprzednich sekcjach. Również wykorzystano  oprogramowanie  środowiska wirtualnego Oracle Virtual Box. Utworzono trzy maszyny wirtualne. Na pierwszej z nich zainstalowano system operacyjny Kali Linux, zawierający pakiet Metasploit. Maszyna ta została wykorzystana jako potencjalne urządzenie atakujące. Na kolejnej maszynie wirtualnej zainstalowano system operacyjny Linux Ubuntu z oprogramowanie Snort z dodatkową wtyczką AI pełniący funkcję maszyny atakowanej.  Na trzecim urządzeniu wirtualnym z systemem Linux Ubuntu zainstalowano oprogramowanie Ostinato jako generator ruchu neutralnego.   

Zaobserwowano spadek liczby fałszywych alertów FPR o ponad 50% w stosunku do testów bez modułu AI. Odchylenie standardowe wyników testu z modułem sztucznej inteligencji nie różni się znacząco w stosunku do testów bez modułu sztucznej inteligencji, co świadczy o powtarzalności pomiarów w poszczególnych seriach oraz w obu testach. Dla pierwszej próby z modułem AI obserwujemy spadek FNR w przypadku wykorzystania modułu sztucznej inteligencji. W pozostałych próbach wyniki odbiegają w mniejszym stopniu. Największy wpływ modułu AI na spadek FPR obserwujemy dla testowanych protokołów: ICMP, ARP, DoS/DDoS oraz FTP.


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkwMzI4OTE4N119
-->
