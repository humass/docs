# Задания Spark

{{ dataproc-name }} поддерживает выполнение заданий [Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) в рамках Spark-_приложений_ (applications).

## Управление приложениями {#applications}

В кластере может быть запущено несколько приложений одновременно. Запущенным приложением управляет специальная программа — _драйвер_ (driver). Драйвер работает в одном из режимов:

* на одном из узлов кластера (`deploy-mode=cluster`);
* отдельно от кластера (`deploy-mode=client`).

Подробнее о режимах работы драйвера см. в [документации Apache Spark](https://spark.apache.org/docs/latest/running-on-yarn.html#launching-spark-on-yarn).

Приложение может находиться в режиме ожидания, либо выполнять _задания_ (jobs). По умолчанию задания в рамках одного приложения выполняются последовательно (_FIFO_). Этот метод не требует дополнительных настроек.

Чтобы включить параллельное выполнение заданий (_FAIR_), необходимо переключить режим работы планировщика, а также дополнительно настроить систему и конкретные задания. О том, как это сделать, читайте в [документации Apache Spark](https://spark.apache.org/docs/latest/job-scheduling.html#scheduling-within-an-application).

{% note tip %}

Чтобы реализовать параллельное выполнение заданий, не используя FAIR, запустите задания в разных Spark-приложениях в режиме FIFO.

{% endnote %}

## Управление операциями {#tasks}

Каждое задание Spark состоит из набора _операций_ (tasks), которые выполняются в рамках специальных программ — _исполнителей_ (executors). Каждый исполнитель запускается на одном узле кластера и занимает определенное количество вычислительных ресурсов (CPU, RAM).

В зависимости от плана выполнения, операции могут выполняться последовательно или параллельно. Параллельно выполняемые операции группируются в _стадии_ (stages). Количество параллельно выполняемых операций зависит от потребностей запросов и от доступных ресурсов кластера.

При использовании стандартных настроек {{ dataproc-name }} вычислительные ресурсы на выполнение заданий Spark выделяются динамически. Чтобы вручную задать объем вычислительных ресурсов, при запуске приложения необходимо передать в Spark настройки:

* Для приложения в целом — `spark.driver.cores`, `spark.driver.memory`.
* Для каждого исполнителя в отдельности — `spark.executor.cores`, `spark.executor.memory`.

## Полезные ссылки {#see-also}

В документации Apache Spark приведены подробные сведения:

* [о настройках Spark-приложений](https://spark.apache.org/docs/latest/configuration.html);
* [о выделении ресурсов для Spark-приложения](https://spark.apache.org/docs/latest/running-on-yarn.html#resource-allocation-and-configuration-overview).

Чтобы проверить работу Spark-приложений в кластере {{ dataproc-name }}, [проведите мониторинг состояния Spark-приложений](../operations/spark-monitoring.md).

Если Spark-приложение работает медленно, [проведите первичную диагностику производительности](../operations/spark-diagnostics.md#diagnostics).
