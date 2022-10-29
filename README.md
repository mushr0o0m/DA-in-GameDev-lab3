# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Филоник Кирилл Русланович
- РИ210931

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

- После настройки объекта был добавлен файл конфигурации [нейронной сети](https://github.com/mushr0o0m/DA-in-GameDev-lab3/blob/main/Meta-files/rollerball_config.yaml) и запущена работа ml-агента и симуляция сцены.

![image](https://user-images.githubusercontent.com/105949115/198356070-6a597762-ab08-4445-b534-493258d536ae.png)

![image](https://user-images.githubusercontent.com/105949115/198355941-78e21dfd-4dda-4bac-97ea-6005393fd02a.png)

- После того как модель обучилась на 90 тыс. итераций, получившаяся модель была протестирована.

![Screen recording](https://github.com/mushr0o0m/DA-in-GameDev-lab3-/blob/main/Meta-files/RollerBall-correct.gif)

- Вывод: В ходе работы была совершена ошибка и получена неверно обучившаяся модель, у элементов сцены были неправильно заданы координаты. Так получилось увидеть некорректное поведение модели после обучения. Ошибка была исправлена и получена корректая модель, в ходе ее обучения было замечено, количество итераций, сделанных при обучении, и количество ошибок обратно пропорцианально. Далее идет демонстрация некорректной модели

![Screen recording](https://github.com/mushr0o0m/DA-in-GameDev-lab3-/blob/main/Meta-files/RollerBall-incorrect.gif) 

## Задание 2
### Подробно описать каждую строку файла конфигурации нейронной сети и найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.

- Файл конфигурации:

```yml

behaviors:
  RollerBall:
    trainer_type: ppo # Эта строка указывает тип алгоритма,
    # в данном случае используется алгоритм Proximal Policy Optimization,
      # он включает в себя накопление опыта взаимодействия с окружающей средой
      # и использование его для обновления политики принятия решений.
    # После ее обновления предыдущие события отбрасываются,
    # а последующий сбор данных осуществляется уже на условиях обновленной политики.
    hyperparameters: # Раздел задающий параметры для алгоритма выбранного выше
      batch_size: 10 # Количество опытов в каждой итерации градиентного спуска.
      buffer_size: 100 # В PPO это количество опыта, который необходимо собрать до обновления модели политики. 
      # Cоответствует тому, сколько опыта должно быть собрано до того, как мы проведем обучение или обновление модели. 
      learning_rate: 3.0e-4 #  Начальная скорость обучения для градиентного спуска.
      # Соответствует силе каждого шага обновления градиентного спуска.
      beta: 5.0e-4 # Сила регуляризации энтропии, которая делает политику "более случайной".
      # Это гарантирует, что агенты правильно исследуют пространство действий во время обучения.
      # Увеличение этого параметра обеспечит более случайные действия
      epsilon: 0.2 # Влияет на скорость изменения политики во время обучения. 
      # Соответствует допустимому порогу расхождения между старой и новой политикой при обновлении методом градиентного спуска.  
      lambd: 0.99 # Параметр регуляризации, используемый при вычислении обобщенной оценки преимущества (GAE).
      # Этот параметр можно представить как то, насколько агент полагается 
      # на свою текущую оценку стоимости при расчете обновленной оценки стоимости.
      # Низкие значения соответствуют тому, чтобы больше полагаться на текущую оценку стоимости,
      # а высокие значения соответствуют тому,
        # чтобы больше полагаться на фактическое вознаграждение, полученное в окружающей среде.
        # Параметр обеспечивает компромисс между этими двумя параметрами,
        # и правильное значение может привести к более стабильному процессу обучения.
      num_epoch: 3 # Количество проходов через буфер опыта при выполнении оптимизации градиентного спуска.
      # Чем больше размер batch_size, тем больше допустимо проходов.
      # Уменьшение этого параметра обеспечит более стабильные обновления, ценой более медленного обучения.
      learning_rate_schedule: linear # Определяет, как скорость обучения изменяется со временем.
      # linear линейно уменьшает скорость обучения, достигая 0 при max_шагах, а constant поддерживает скорость обучения 
    network_settings: # Сетевые характеристики для модели
      normalize: false # Применяется ли нормализация к входам векторного наблюдения.
      # Эта нормализация основана на текущем среднем и дисперсии векторного наблюдения.
      # Нормализация может быть полезной в случаях со сложными непрерывными проблемами управления,
        # но может быть вредной в более простых дискретных проблемах управления.
      hidden_units: 128 # Количество единиц в скрытых слоях нейронной сети.
      # Соответствует количеству единиц в каждом полностью подключенном слое нейронной сети
      num_layers: 2 # Количество скрытых слоев в нейронной сети.
      # Соответствует тому, сколько скрытых слоев присутствует
      # после входа наблюдения или после кодирования CNN визуального наблюдения.
    reward_signals: # Раздел позволяющий задать параметры для внешних и внутренних сигналов вознаграждения
      extrinsic: # Раздел, который позволяет задать/включить настройки вознагрождения на основе окружающей среды
        gamma: 0.99 # Коэффициент дисконтирования для будущих вознаграждений,
        # поступающих из окружающей среды. Он определяет как будет действовать агент для 
        strength: 1.0 # Коэффициент, на который умножается вознаграждение, выдаваемое средой.
        # Типичные диапазоны зависят от сигнала вознаграждения
    max_steps: 500000 # Общее количество шагов, которые необходимо выполнить в среде перед завершением процесса обучения
    time_horizon: 64 # Это количество шагов которое необходимо сделать агенту,
    # чтобы добавить его в буфер опыта.
    # Этот параметр балансирует дисперстную оценку
    summary_freq: 10000 # Количество опытов которое необходимо провести,
    # чтобы появилась статистика обучения в командной строке

```

- Компонент Decision Requester
 - Компонент DecisionRequester предоставляет удобный и гибкий способ для того, чтобы запускать процесс принятия решения агентом. 

![image](https://user-images.githubusercontent.com/105949115/198738829-49231fa6-6f33-4d73-b1d5-e1f2a4c379db.png)



- Компонент Behavior Parameters
 - Компонент для установки поведения экземпляра и свойств интеллекта. Во время выполнения этот компонент генерирует объекты политики агента. в соответствии с настройками, заданными в редакторе.

![image](https://user-images.githubusercontent.com/105949115/198739617-1e50d034-fafc-4cd8-8779-b503eb8ce4ca.png)

## Задание 3
### Дороботать сцену и обучить ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости

- Первым шагом был создан еще один куб, на него добавлен материал, который задавал зеленый цвет. Также был создан пустой объект, это новый Target. По умолчанию он распологается между двумя кубами, так мы можем видеть точку через которую должен пройти шар, после того, как кубы изменят свою позицию.

![image](https://user-images.githubusercontent.com/105949115/198836335-d93887b0-e0fc-4419-8754-3133e36f8850.png)

- Далее был изменен скрипт RollerAgent.cs в соответсвии с новой задачей. Теперь в методе OnEpisodeBegin() генерируется не только позиция первого куба но и второго, а позиция target`a вычисляется как середина между координатми firstCube и secondCube. Также было изменено расстаяние, на которое нужно приблизиться шару, чтобы получить вознограждение.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    [SerializeField] private float forceMultiplier = 10;
    [SerializeField] private Transform firstCube;
    [SerializeField] private Transform secondCube;
    [SerializeField] private Transform target;
    private Rigidbody rBody;

    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        firstCube.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
        secondCube.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
        target.localPosition = new Vector3(
            (firstCube.localPosition.x + secondCube.localPosition.x) / 2,
            0.5f,
            (firstCube.localPosition.z + secondCube.localPosition.z) / 2);
    }

    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, target.localPosition);

        if(distanceToTarget < 1.2f)
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

-  Затем сцена была добавлена в Prefab и созданы ее копии, для того чтобы начать обучение. Оно заняло ~27 мин., после чего я проверил работу модели.

![image](https://user-images.githubusercontent.com/105949115/198836830-6d1fa78d-5d1e-4aa4-ae9d-089a6ee57b42.png)

- Демонстрация работы модели.

![Screen recording](https://github.com/mushr0o0m/DA-in-GameDev-lab3/blob/main/Meta-files/20221029_193012.gif)


## Выводы

Что такое игровой баланс? Это баланс характеристик объектов игрового взаимодействия. Описания баланса зависит от типа игры, и от объектов которые необходимо балансировать. Абсолютного баланса найти невозможно, так как это не точный набор данных, который можно посчитать, а представление, которое формируется как и математическими вычислениями, так и количеством проанализированных игровых сессий.

Как системы машинного обучения могут быть использованы для того, чтобы его скорректировать? Машинное обучение может помочь анализировать игровые сессии игроков и генерировать оптимальные уровни или противников. В однопользовательских играх машинное обучение может изменять сложность игровых испытаний и поведение вражеских юнитов в зависимости от поведения игрока и количества попыток, которые были им затрачены. Каждый из сценариев изпользования машинного обучения в балансировке игры, будет зависить от конкретной игры и объектов которые необходимо уравновесить, как я и писал выше.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
