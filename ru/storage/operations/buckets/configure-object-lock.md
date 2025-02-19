# Управление блокировками версий объектов (object lock) в бакете

В [версионируемых](versioning.md) бакетах можно настроить механизм _[блокировки версий объектов](../../concepts/object-lock.md)_ (object lock). Когда он включен, вы можете установить на версию объекта блокировку, то есть запрет на удаление или перезапись. Также для бакета можно настроить блокировки по умолчанию: они будут устанавливаться на все новые версии объектов.

## Включить возможность блокировок {#enable}

Включение механизма блокировок не устанавливает блокировки на уже загруженные версии объектов, а только позволяет устанавливать их.

Минимальная необходимая роль — `storage.admin`.

Чтобы включить возможность блокировок:

{% list tabs %}

- AWS CLI

  Если у вас еще нет AWS CLI, [установите и сконфигурируйте его](../../tools/aws-cli.md).
 
  Выполните следующую команду:

  ```bash
  aws --endpoint-url=https://{{ s3-storage-host }}/ \
    s3api put-object-lock-configuration \
    --bucket <имя_бакета> \
    --object-lock-configuration ObjectLockEnabled=Enabled
  ```
     
  Где:
 
  * `bucket` — имя бакета.
  * `object-lock-configuration` — настройки блокировок в бакете:

    * `ObjectLockEnabled=Enabled` — механизм блокировок выключен.

- API

  Воспользуйтесь методом REST API [update](../../api-ref/Bucket/update.md) для ресурса [Bucket](../../api-ref/Bucket/index.md), вызовом gRPC API [BucketService/Update](../../api-ref/grpc/bucket_service.md#Update) или методом S3 API [putObjectLockConfiguration](../../s3/api-ref/bucket/putobjectlockconfiguration.md).

{% endlist %}


## Настроить блокировки по умолчанию {#default}

Блокировки по умолчанию устанавливаются на все новые версии объектов, загружаемые в бакет. Настройки не влияют на уже загруженные версии.

Минимальная необходимая роль — `storage.admin`.

Чтобы настроить блокировки по умолчанию:

{% list tabs %}

- AWS CLI

  Если у вас еще нет AWS CLI, [установите и сконфигурируйте его](../../tools/aws-cli.md).

  1. Опишите конфигурацию блокировок по умолчанию в формате JSON:
 
     ```json
     {
       "ObjectLockEnabled": "Enabled",
       "Rule": {
         "DefaultRetention": {
           "Mode": "<тип_блокировки>",
           "Days": <срок_блокировки_в_днях>,
           "Years": <срок_блокировки_в_годах>
         }       
       }
     }
     ```
     
     Где:
 
     * `ObjectLockEnabled` — статус механизма блокировок: `Enabled` — механизм включен.
       
       {% note alert %}
 
       Это обязательное поле. Если не указать его со значением `Enabled`, механизм блокировок отключится.
 
       {% endnote %}

     * `Mode` — [тип](../../concepts/object-lock.md#types) блокировки:
 
       * `GOVERNANCE` — временная управляемая блокировка.
       * `COMPLIANCE` — временная строгая блокировка.
 
     * `Days` — срок блокировки в днях от момента загрузки версии объекта. Должен быть положительным целым числом. Нельзя указывать вместе с `Years`.
     * `Years` — срок блокировки в годах от момента загрузки версии объекта. Должен быть положительным целым числом. Нельзя указывать вместе с `Days`.
 
     Готовую конфигурацию можно поместить в файл, например, `default-object-lock.json`.
 
  1. Загрузите конфигурацию в бакет:
 
     ```bash
     aws --endpoint-url=https://{{ s3-storage-host }}/ \
       s3api put-object-lock-configuration \
       --bucket <имя_бакета> \
       --object-lock-configuration file://default-object-lock.json
     ```
     
     Где:
 
     * `bucket` — имя бакета.
     * `object-lock-configuration` — настройки блокировок по умолчанию. В данном случае указаны в файле `default-object-lock.json`.

{% endlist %}

## Выключить возможность блокировок {#disable}

Выключение механизма блокировок не снимает установленные блокировки: они продолжают работать, их нельзя снять или изменить.

Минимальная необходимая роль — `storage.admin`.

Чтобы выключить возможность блокировок:

{% list tabs %}

- AWS CLI

  Если у вас еще нет AWS CLI, [установите и сконфигурируйте его](../../tools/aws-cli.md).
 
  Выполните следующую команду:
 
  ```bash
  aws --endpoint-url=https://{{ s3-storage-host }}/ \
    s3api put-object-lock-configuration \
    --bucket <имя_бакета> \
    --object-lock-configuration ""
  ```
 
  Где:
 
  * `bucket` — имя бакета.
  * `object-lock-configuration` — настройки блокировок в бакете. Пустое значение отключает механизм блокировок.

- API

  Чтобы выключить возможность блокировок версий объектов в бакете, воспользуйтесь методом REST API [update](../../api-ref/Bucket/update.md) для ресурса [Bucket](../../api-ref/Bucket/index.md) или вызовом gRPC API [BucketService/Update](../../api-ref/grpc/bucket_service.md#Update).

{% endlist %}
