# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Филоник Кирилл Русланович
- РИ210931

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

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
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity

- Создан 3D проект на Unity, к нему был добавлен пакет ML Agent. Далее в сцене было добавлено три объекта (Floor, Target, RollerAgent)

![image](https://user-images.githubusercontent.com/105949115/198351905-7809f929-0449-4517-b433-a433f1b95260.png)

- С помощью Anaconda Prompt, через консоль были добавлены все необходимые библиотеки (mlagents 0.28.0 и torch 1.7.1)

  - Создание, установка и активация ML Agent в командной строке
  
  ![image](https://user-images.githubusercontent.com/105949115/198352946-7155f46a-9632-4a36-a97a-16f9837e5748.png)
  
  ![image](https://user-images.githubusercontent.com/105949115/198353501-f98e3d23-fb6b-4bd5-8bc7-8d5da4b2f6b0.png)
  
  - Установка библиотеки torch 1.7.1

  ![image](https://user-images.githubusercontent.com/105949115/198354110-2833ebab-ab14-4406-a1b2-ee632d9ae15b.png)

- Объекту на сцене RollerAgent были добавлены и настроены 3 компанента и CS-скрипт

  - Компоненты объекта:
  
  ![image](https://user-images.githubusercontent.com/105949115/198354743-c37c286d-049c-45ac-8f12-aa8e7c5669e7.png)
  
  - Код скрипта RollerAgent.cs:

```cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}

```

- После настройки объекта был добавлен файл конфигурации [нейронной сети]() и запущена работа ml-агента и симуляция сцены.

![image](https://user-images.githubusercontent.com/105949115/198356070-6a597762-ab08-4445-b534-493258d536ae.png)

![image](https://user-images.githubusercontent.com/105949115/198355941-78e21dfd-4dda-4bac-97ea-6005393fd02a.png)

- После того как модель обучилась на 90 тыс. итераций, получившаяся модель была протестирована.

![Screen recording]()

- Вывод: В ходе работы была совершена ошибка и получена неверно обучившаяся модель, у элементов сцены были неправильно заданы координаты. Так получилось увидеть некорректное поведение модели после обучения. Ошибка была исправлена и получена корректая модель, в ходе ее обучения было замечено, количество итераций, сделанных при обучении, и количество ошибок обратно пропорцианально. Далее идет демонстрация некорректной модели

![Screen recording]() 

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1.




## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.



## Выводы



## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
