# Camera Tracking: sledovanie objektu kamerou (STM32)

Semestrálny projekt z predmetu **Vnorené riadiace systémy (VRS)**, FEI STU Bratislava, ZS 2026/27.

**Autor:** Adam Eliaš (samostatný projekt)
**
**
Platforma:** STM32H743 (doska WeAct MiniSTM32H7xx) s rozhraním DCMI, jazyk C (STM32CubeIDE), spracovanie obrazu priamo na mikrokontroléri

---

## 1. Popis projektu

Cieľom je postaviť kameru na pan-tilt mechanizme, ktorá sa sama natáča za vybraným objektom a drží ho v strede záberu. Kamerový modul posiela obraz cez rozhranie DCMI priamo do STM32. Mikrokontrolér v obraze nájde objekt, vypočíta jeho odchýlku od stredu a podľa nej v reálnom čase riadi dve servá.

Celé spracovanie obrazu, regulácia aj prepínanie režimov bežia na STM32, takže zariadenie funguje samostatne bez PC. Počítač slúži iba na príjem telemetrie a vyhodnotenie meraní.

### Funkcie

1. **Snímanie obrazu** cez DCMI s prenosom do pamäte pomocou DMA.
2. **Detekcia objektu** priamo na STM32 (prahovanie, výpočet ťažiska).
3. **Regulácia natočenia** v osiach pan a ti..
4. **Obmedzenie pohybu:** rozsah uhlov a maximálna rýchlosť natáčania.
5. **Režimy:** nečinnosť, sledovanie, vyhľadávanie a manuálne ovládanie.
6. **Vyhľadávanie** objektu po jeho strate zo záberu.
7. **Telemetria do PC** cez UART (poloha objektu, odchýlka, uhly serv, čas).

### Bloková schéma

    Kamera OV7725 --DCMI+DMA--> STM32 (detekcia -> regulátor) --PWM--> servá pan/tilt
                                   |
                                   +--UART--> PC (Python: logovanie, grafy)

---

## 2. Ciele a overenie

| Cieľ | Kritérium | Spôsob overenia |
|---|---|---|
| Udržanie statického objektu v strede | ustálená odchýlka ≤ X px | telemetria, graf odchýlky |
| Sledovanie pohybujúceho sa objektu | bez straty pri uhlovej rýchlosti do Y °/s | test s objektom v známej rýchlosti |
| Opätovné nájdenie objektu | do Z s po návrate do zorného poľa | meranie z telemetrie |
| Spracovanie v reálnom čase | ≥ N snímok za sekundu | časové značky v telemetrii |

*(Hodnoty X, Y, Z, N doplním po prvých meraniach.)*

---

## 3. Hardvér

| Komponent | Ks | Účel |
|---|---|---|
| WeAct MiniSTM32H743 (STM32H743VIT6) | 1 | riadiaca jednotka a spracovanie obrazu |
| Kamerový modul OV7725, objektív M12 3,6 mm | 1 | snímanie obrazu |
| FFC kábel 24 pin, rozteč 0,5 mm, 20 cm | 1 | prepojenie kamery s doskou |
| Servo SG90 | 2 | otáčanie v osiach pan a tilt |
| Pan-tilt držiak pre SG90 | 1 | uchytenie kamery |
| Box na 4× AA NiMH (4,8 V) | 1 | napájanie serv |
| ST-Link V2 | 1 | programovanie a ladenie (SWD) |
| Tlačidlo, vodiče, kontaktné pole | – | ovládanie a zapojenie |

Všetky súčiastky si zabezpečujem sám.

### Zapojenie

*(Doplnlo.)*

---

## 4. Softvér

- **STM32:** C, STM32CubeIDE a CubeMX (HAL)
- **PC:** Python na logovanie telemetrie a grafy
- **Správa kódu:** Git a GitHub

---

## 5. Rozdelenie práce

Projekt robím sám, všetky časti mám na starosti ja.

---

## 6. Aktuálny stav

- [x] Špecifikácia projektu
- [x] Výber a nákup hardvéru
- [ ] Rozbehnutie DCMI a zachytenie snímky
- [ ] Ovládanie serv (PWM)
- [ ] Detekcia objektu
- [ ] Regulátor a režimy
- [ ] Telemetria a vyhodnotenie