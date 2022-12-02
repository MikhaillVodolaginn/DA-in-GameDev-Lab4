# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил:
- Водолагин Михаил Алексеевич
- РИ210942
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
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
Познакомиться с работой перцептрона с помощью Unity.

## Задание 1
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке.
- Создал новый проект Unity, создал в нем пустой объект и прикрепил к нему скрипт C# Perceptron.cs
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2019.32.02.png?raw=true)
- Логическая операция OR
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2019.41.35.png?raw=true)
По итогу ошибка составила 0 и перцептрон смог обучиться, на тестовых данных работает верно
- Логическая операция AND
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2019.37.50.png?raw=true)
По итогу ошибка составила 0 и перцептрон смог обучиться, на тестовых данных работает верно
- Логическая операция NAND
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2019.43.47.png?raw=true)
По итогу ошибка составила 0 и перцептрон смог обучиться, на тестовых данных работает верно
- Логическая операция XOR
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2019.45.58.png?raw=true)
По итогу ошибка оказалась больше 0 и перцептрон не смог обучиться, на тестовых данных работает неверно.
Это связано с тем, перцептрон проводит так называемую границу между подходящими и неподходящими данными, а для данной операции необходимо 2 так называемые границы, то есть как минимум 2 перцептрона, работающих в связке

## Задание 2
### Построить графики зависимости. Указать от чего зависит необходимое количество эпох обучения.

Я построил в excel таблицу с величинами ошибки для каждой операции на каждой итерации, на основании этих данных я построил графики зависимости величины ошибки обучения от номера эпохи
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2020.02.06.png?raw=true)
- По этим графикам можно сказать, что перцептрон действует по принципу "работает - не меняем". Если он получает ошибку 0 на какой-либо итерации, то перестает экспериментировать с коэффициентами.
- Если перцептрон способен обучиться (все операции, кроме XOR), он обучается абсолютно рандомно. Для OR ему вообще повезло, и он с первого же раза подобрал подходящие коэффициенты, с AND и NAND повезло меньше.
- В ходе экспериментов, пока ошибка не достигла 0, она может, как уменьшаться, так и увеличиваться
- Если строить сплошной график в excel с настройками по умолчанию, некоторые части графика в результате сглаживания могут выходить за пределы возможного количества эпох, хотя это лишь визуально неправильно, ведь у нас не может быть 4,5 эпох. Это очень просто решается точечным графиком
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab4/blob/main/Снимок%20экрана%202022-12-02%20в%2020.02.06.png?raw=true)

## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости.

- Измененный C# скрипт
```
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public GameObject Target1;
    public GameObject Target2;
    private bool target1Collected;
    private bool target2Collected;

    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }
        Target1.transform.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        Target2.transform.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        Target1.SetActive(true);
        Target2.SetActive(true);
        target1Collected = false;
        target2Collected = false;
    }

    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target1.transform.localPosition);
        sensor.AddObservation(Target2.transform.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(target1Collected);
        sensor.AddObservation(target2Collected);
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
        float distanceToTarget1 = Vector3.Distance(this.transform.localPosition, Target1.transform.localPosition);
        float distanceToTarget2 = Vector3.Distance(this.transform.localPosition, Target2.transform.localPosition);
        if (!target1Collected & distanceToTarget1 < 1.42f)
        {
            target1Collected = true;
            Target1.SetActive(false);
        }
        if (!target2Collected & distanceToTarget2 < 1.42f)
        {
            target2Collected = true;
            Target2.SetActive(false);
        }
        if (target1Collected & target2Collected)
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

- Обучение нового скрипта, среднее вознаграждение 0,989
[![](https://github.com/MikhaillVodolaginn/DA-in-GameDev-Lab3/blob/main/Снимок%20экрана%202022-11-01%20в%2016.35.42.png?raw=true)

## Выводы

В данной лабораторной работе я научился подключать mlagent и pytorch, а также впервые работал в консоли с помощью анаконды.

Игровой баланс - одно из важнейших понятий в играх, так как от него зависит равенство взаимодействий тех или иных объектов, правил, механик.

Хороший баланс должен поддерживать не слишком простой уровень сложности, чтобы не было слишком скучно и не слишком высокий, чтобы не возникало отторжения. Игрок должен ощущать, что игра честна по отношению к нему. В играх существует огромное разнообразие объектов и добиться синергии между ними очень сложно, особенно в асимметричных играх, где у каждого игрока качественно разные способности и стили игры, необходимо учесть много факторов, однако нейросети могут с этим помочь, например, могут подстраивать степень сложности окружающего мира под игрока, поэтому развивая себя игрок одновременно развивает и мир вокруг. Нейросети могут анализировать пикрейт персонажей в многопользовательской игре и уменьшать характеристики тех героев, на которых он повышен

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

Vodolagin Mikhail
