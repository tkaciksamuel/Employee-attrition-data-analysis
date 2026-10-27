# Variant 5 — HR Workforce Analytics

*(analýza personálu, fluktuácie a efektívnosti náboru)*

> Tento priečinok obsahuje zadanie a reálne dáta.

## Biznis scenár

Spoločnosť aktívne rastie a najíma zamestnancov do viacerých divízií, no čelí
nerovnomernej fluktuácii, dlhému uzatváraniu pozícií a rôznej efektívnosti
náborových kanálov. Manažment chce pochopiť, ktoré tímy majú vyššie riziko odchodu
zamestnancov, ktoré fázy náboru trvajú najdlhšie a ktoré kanály získavania
kandidátov prinášajú najlepšie výsledky. HR riaditeľ očakáva od analytika report so
zisteniami a odporúčaniami na zníženie fluktuácie, urýchlenie náboru a zlepšenie
plánovania pracovnej sily.

## Hlavný cieľ projektu

Vyvinúť Python analytické riešenie na analýzu personálnych, náborových dát a dát o
ukončení pracovného pomeru na základe sady dátových súborov — načítanie, čistenie,
zlučovanie, analýza, vizualizácie a stručné biznis závery pre manažment.

## Kľúčoví účastníci systému
- Dátový analytik
- HR riaditeľ
- HR manažér náboru
- Vedúci divízie
- Prevádzkový manažér

## Je potrebné pracovať s informáciami o
- zamestnancoch (divízia, pozícia, dátum nástupu, stav),
- ukončeniach pracovného pomeru (dátum, dôvod, typ ukončenia),
- voľných pozíciách a fázach náboru,
- kandidátoch a zdrojoch ich získania,
- termínoch uzatvárania pozícií,
- hodnoteniach ukončenia skúšobnej doby.

## Predpokladané možnosti (etapy 1–5)

1. **Načítanie a príprava dát** — min. 3 súbory (napr. employees.csv,
   vacancies.csv, candidates.csv), kódovanie/oddeľovače, typy dát, chýbajúce
   hodnoty, duplicity, nesprávne hodnoty (dátum ukončenia skôr než nástup, záporná
   dĺžka uzatvárania pozície, prázdne ID pozície).
2. **Zlučovanie a transformácia dát** — `merge` podľa kľúča, jednotný analytický
   dataset personálu a náboru, nové atribúty (dĺžka zamestnania v dňoch/mesiacoch,
   mesiac nástupu/odchodu, príznak odchodu v skúšobnej dobe, dĺžka uzatvárania
   pozície, príznak úspešného náboru podľa zdroja).
3. **Analýza dát (EDA)** — počet zamestnancov, počet odchodov, podiel fluktuácie za
   obdobie, priemerná dĺžka zamestnania, počet otvorených/uzavretých pozícií,
   priemerný čas uzatvorenia pozície, metriky podľa zdroja kandidátov, podľa
   divízie a pozície, detekcia anomálií.
4. **Vizualizácia dát** — min. 5 grafov (línia trendu náboru/odchodov v čase, bar
   podľa divízie, porovnanie náborových zdrojov, bar podľa pozície/divízie
   (priemerný čas uzatvorenia), histogram dĺžky uzatvárania pozícií, boxplot na
   odľahlé hodnoty, + voliteľný graf).
5. **Formulácia výsledkov a záverov** — min. 3 pozorovania, min. 2 odporúčania
   (zníženie fluktuácie, zlepšenie náborových kanálov, urýchlenie uzatvárania
   pozícií, plánovanie pracovnej sily).

---

## Reálny verejný dataset

Použitý dataset: **IBM HR Analytics Employee Attrition & Performance** (Kaggle,
klasický a široko používaný HR dataset), tu stiahnutý z verejného GitHub mirroru.

| Súbor | Zdroj | Popis | Riadky |
|---|---|---|---|
| `data/raw/ibm_hr_employee_attrition.csv` | [nelson-wu/employee-attrition-ml](https://github.com/nelson-wu/employee-attrition-ml) (pôvodne [Kaggle: pavansubhasht/ibm-hr-analytics-attrition-dataset](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset)) | Zamestnanci s atribútmi oddelenia, pozície, dĺžky zamestnania, spokojnosti a fluktuácie (Attrition) | 1 470 |

### Stĺpce (35)
`Age, Attrition, BusinessTravel, DailyRate, Department, DistanceFromHome,
Education, EducationField, EmployeeCount, EmployeeNumber,
EnvironmentSatisfaction, Gender, HourlyRate, JobInvolvement, JobLevel, JobRole,
JobSatisfaction, MaritalStatus, MonthlyIncome, MonthlyRate, NumCompaniesWorked,
Over18, OverTime, PercentSalaryHike, PerformanceRating, RelationshipSatisfaction,
StandardHours, StockOptionLevel, TotalWorkingYears, TrainingTimesLastYear,
WorkLifeBalance, YearsAtCompany, YearsInCurrentRole, YearsSinceLastPromotion,
YearsWithCurrManager`

### Mapovanie na požadované entity zadania

| Požadovaná entita | Reálny zdroj | Poznámka |
|---|---|---|
| Zamestnanci (divízia, pozícia, nástup, stav) | `Department`, `JobRole`, `JobLevel`, `Attrition` | Priamo dostupné. **Chýba konkrétny dátum nástupu** — dataset dáva `YearsAtCompany` (dĺžku, nie kalendárny dátum); dátum nástupu sa dá len odvodiť/aproximovať od referenčného dátumu extraktu (zdokumentovaný predpoklad). |
| Ukončenia pracovného pomeru (dátum, dôvod, typ) | `Attrition` (Yes/No) | Iba binárny príznak, **bez dátumu a bez dôvodu odchodu** — zdokumentovaná medzera; analýza fluktuácie sa robí ako "podiel Attrition=Yes" podľa divízie/pozície, nie ako časová dynamika day-by-day. |
| Voľné pozície a fázy náboru | **úplne chýba** | Dataset je „post-hire" (o existujúcich zamestnancoch), neobsahuje žiadnu recruitment-funnel entitu. Zdokumentovaná medzera — reálny verejný dataset s presne touto štruktúrou (vacancy → candidate → hire, s dátumami fáz) sa nepodarilo nájsť voľne stiahnuteľný bez Kaggle prihlásenia. Pre plnohodnotné riešenie tejto časti zadania treba doplniť malú, transparentne označenú syntetickú tabuľku `vacancies.csv`/`candidates.csv`. |
| Kandidáti a zdroje získania | **chýba** | Rovnaká medzera ako vyššie. |
| Termíny uzatvárania pozícií | **chýba** | Rovnaká medzera ako vyššie. |
| Hodnotenie skúšobnej doby | **chýba priamo**, čiastočná proxy | `PerformanceRating` a `JobSatisfaction`/`EnvironmentSatisfaction` existujú ako všeobecné hodnotenia výkonu/spokojnosti, ale nie špecificky viazané na koniec skúšobnej doby. |

> **Poznámka:** Zo všetkých 5 variantov má tento najväčšiu medzeru medzi zadaním a
> dostupnými reálnymi dátami (recruitment-funnel časť). Odporúčanie: v riešení jasne
> oddeliť "časť A – Attrition/Workforce" (plne pokrytá reálnymi dátami z IBM HR
> datasetu) od "časti B – Recruitment funnel" (vyžaduje doplnkovú syntetickú tabuľku
> alebo zúženie rozsahu zadania a explicitné odôvodnenie v záveroch).
