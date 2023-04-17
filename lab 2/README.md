---
title: "Сбор и аналитическая обработка информации о сетевом трафике"
format: md
author: "Elizaveta Rozantseva"
date: "17.04.2023"
editor: visual
---

## Получение сведений о системе

## Цель работы

1.Развить практические навыки использования современного стека инструментов сбора и аналитической обработки информации о сетевом трафике

2.Освоить базовые подходы блокировки нежелательного сетевого трафика

3.Закрепить знания о современных сетевых протоколах прикладного уровня

## Ход работы

1.  С помощью команды zeek -i enp0s3 mypackets.trace был захвачен трафик

    ```{bash}
    zeek -i enp0s3 mypackets.trace
    ```


2.  С помощью утилиты был произведен анализ захваченного трафика:

    ```{bash}
    zeek –C –r mypackets.trace
    ```


3.  Для анализа трафика необходимо получить список нежелательных доменов:

    ```{bash}
    curl https://winhelp2002.mvps.org/hosts.txt -o hosts
    ```

    <div>

       

    </div>

    С помощью Python посчитаем кол-во нежелательных доменов:

    ```{python}
    bad_hosts = []
    with open('hosts') as file:
        for line in file.readlines()[27:]:
            if line[0] == '#':
                continue
            bad_hosts.append(line.split()[1])
    ```

    Получим кол-во всех доменов из анализируемого файла:

    ```{python}
    hosts = []
    with open('dns.log') as file:
        for line in file.readlines():
            if line[0] == '#':
                continue
            try:
                hosts.append(line.split()[9])
            except IndexError:
                continue
    ```

4.  Получим вывод в процентном соотношении о наличии нежелательного трафика:

    ```{python}
    bad_count = len([host for host in hosts if host in bad_hosts])
    percentile = round(bad_count/len(hosts),3)*100
    print("Число вхождений DNS в список нежелательного трафика: {}.".format(str(bad_count)),
    "Процент нежелательного трафика: {}%.".format(str(percentile)),sep='\n')
    ```

5.  Число вхождений DNS в список нежелательного трафика: 624.
    Процент нежелательного трафика: 17.9%.
    
    

## Вывод

Таким образом была произведена работа с утилитой Zeek и проанализирован сетевой трафик, из которого был выявлен нежелательный