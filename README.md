# DA-in-GameDev-lab2
# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
СБОР, ОБРАБОТКА И ВИЗУАЛИЗАЦИЯ ТЕСТОВОГО НАБОРА ДАННЫХ.

Отчет по лабораторной работе #2 выполнил(а):
- Толочко Дмитрий Александрович
- РИ-210944
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity. 
#### 1) В облачном сервисе google console подключить API для работы с google sheets и google drive.
- Создал проект в google cloud 
![image](https://user-images.githubusercontent.com/100460661/194804721-f239b7ee-d051-4cf7-90eb-37b630ba701e.png)

- Подключил google drive api и google sheets api
![image](https://user-images.githubusercontent.com/100460661/194805120-a02cb211-9121-4743-9ed6-9478d9836dbb.png)
![image](https://user-images.githubusercontent.com/100460661/194805191-54ce1d30-b6c7-43e0-b438-afa9bbe67013.png)

- Создал сервисный аккаунт и добавил ссылку-ключ на проект на питоне
![image](https://user-images.githubusercontent.com/100460661/194806148-603dc0d5-ecd8-4b53-bd03-134528cf7e8f.png)
![image](https://user-images.githubusercontent.com/100460661/194806644-7bc31634-cdf8-4017-b018-6e26182a25bf.png)

- Создал google таблицу и открыл доступ сервисному аккаунту
![image](https://user-images.githubusercontent.com/100460661/194808250-8e8e10e4-ac39-4797-a3c0-75b819b30f80.png)

#### 2) Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.
- Запустил программу. Произвелась запись данных в таблицу.
![image](https://user-images.githubusercontent.com/100460661/194809517-e80bb747-d746-450f-82ee-1974b247a37a.png)
![image](https://user-images.githubusercontent.com/100460661/194809541-39747dbb-bec5-42f7-b235-05db10166a5a.png)

#### 3) Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.
- Создал проект и импортировал пакеты
![image](https://user-images.githubusercontent.com/100460661/194811890-7ebebe97-0de6-43e5-bb4a-1fa4e87f744c.png)

#### 4) Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.
- Написал код, который был в методическом материале:
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest currentResp = UnityWebRequest
        .Get("https://sheets.googleapis.com/v4/spreadsheets/17zs3JwOz0K3DCcw_ecP7XllVM2hTsXRSa7t9lmkFOWo/values/Лист1?key=AIzaSyBuaDT5dbePnNnk6u0Q_cFI5_dJWjhopVU");
        yield return currentResp.SendWebRequest();
        string rawResp = currentResp.downloadHandler.text;
        var rawJSON = JSON.Parse(rawResp);
        foreach (var itemRawJSON in rawJSON["values"])
        {
            var parseJSON = JSON.Parse(itemRawJSON.ToString());
            var selectRow = parseJSON[0].AsStringList;
            dataSet.Add("Mon_" + selectRow[0], float.Parse(selectRow[2]));
        }
        //Debug.Log(dataSet["Mon_1"]);
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

- Добавил audio source и инициализировал поля в скрипте

![image](https://user-images.githubusercontent.com/100460661/194817650-8d58330f-1429-45ed-81b3-27929e1a1a6e.png)

- Вывод программы (Звук слышно с каждой строкой в выводе)
![image](https://user-images.githubusercontent.com/100460661/194817736-403220a8-c47d-490b-91b7-9dbbb745f8f9.png)


## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 
- Изменил код из первой лабораторной

```py
import gspread
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b

a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.000001

gc = gspread.service_account(filename='unitydatascience-365105-39836c275fc8.json')
sh = gc.open("UnitySheets")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        a, b = iterate(a, b, x, y, 100)
        prediction = model(a, b, x)
        loss = loss_function(a, b, x, y)
        loss = str(loss).replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(loss))
        print(loss)
```

- Добавил ссылку-ключ на этот проект
![image](https://user-images.githubusercontent.com/100460661/194822184-966fe5e1-45d4-4d19-9470-9a7adb678196.png)

- Запустил код. Произвелась запись данных в таблицу.
![image](https://user-images.githubusercontent.com/100460661/194822709-30675b28-15c5-4b2e-84ca-365ba8c5b410.png)

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.
- Изменил диапазоны при которых проигрываются аудиофайлы
![image](https://user-images.githubusercontent.com/100460661/194823419-7ddbd69e-f994-4333-8fbe-765813c34f0f.png)

- Вывод в Unity (Звук слышно с каждой строкой в выводе)
![image](https://user-images.githubusercontent.com/100460661/194825588-3e09af1f-d125-4fc3-bf51-aba62744ec0e.png)


## Выводы
Я научился работать в связке Unity - Google Sheets - Python, записывать данные в таблицу в интернете и считывать их оттуда, управлять звуками в Unity
| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
