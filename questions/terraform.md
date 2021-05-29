## Terraform

1. Что содержит код Terraform?

<details>
  <summary>Ответ</summary>

Ресурсы облачного провайдера, а также провижининг для создаваемых ресурсов.

</details>

2. Как хранить состояние инфраструктуры в Terraform?

<details>
  <summary>Ответ</summary>

Например, можно хранить tfstate в git-репозитории команды. Другой вариант - хранить в специализированном Terraform Backend.

</details>

3. Terraform Backend. Какой лучше?

<details>
  <summary>Ответ</summary>

Зависит от требованиям к хранению состояния.

- AWS S3 — Standard (с locking через DynamoDB). Сохраняет состояние в виде заданного ключа в заданном сегменте на Amazon S3. Этот бэкэнд также поддерживает блокировку состояния и проверку согласованности через DynamoDB.

- terraform enterprise — Standard (без блокировки).

- etcd — Standard (без лока). Сохраняет состояние в etcd 2.x по заданному пути.

- etcdv3 — Standard (с блокировкой). Сохраняет состояние в хранилище etcd в виде K/V с заданным префиксом.

- gcs — Standard (с локом). Сохраняет состояние как объект в настраиваемом префиксе в заданном сегменте в Google Cloud Storage (GCS). Этот бэкэнд также поддерживает блокировку состояния.

Существуют также и другие Backend для Terraform.

</details>

4. Как добавить имеющиеся ресурсы в tfstate?

<details>
  <summary>Ответ</summary>

```
terraform import [options] ADDRESS ID
```
1. Например, создаем директорию и инициализируем будущую инфраструктуру:
```
mkdir terraform-test
cd terraform-test
terraform init
vi main.tf
```
2. Добавляем в файл main.tf следующий код:
```
provider "aws" {
  region = "us-west-1"
  profile = "tyx-local"
}
resource "aws_s3_bucket" "sample_bucket" {
  bucket = "tyx-local-bucket"
  acl = "public"
}
```
3. Выполняем импорт ресурса:
```
terraform import aws_s3_bucket.sample_bucket tyx-local-bucket
```

</details>

5. Зачем нужен `terraform taint`?

<details>
  <summary>Ответ</summary>

Команда `terraform taint` пометит ресурс инфраструктуры, который будет удален и заново создан при следующем применении команды `terraform apply`. 

</details>

6. Как проводить тестирование terraform?

<details>
  <summary>Ответ</summary>

`terraforn plan` выполнит проверку действующего кода. Работу с облачными ресурсами выполнит 

</details>

7. Что такое модуль в terraform? Для чего он нужен?

<details>
  <summary>Ответ</summary>

Модуль в Terraform - пакет конфигурации Terraform, который можно использовать при повторной конфигурации компонентов инфраструктуры, а также базовой организации кода Terraform в директориях. При подключения модуля, ему даётся имя.

</details>

8. Как хранить переменные в terraform?

<details>
  <summary>Ответ</summary>

*main.tf* - основной конфигурационный файл, описывающий какие инстансы необходимо создать.
*variables.tf* - конфигурация с описанием переменных и значениями по-умолчанию. Если значения по-умолчанию не задано, то они являются обязательными.
*terraform.tfvars* - конфигурация со значениями переменных. Часто является секретным файлом, поэтому нужно с осторожностью пушить в публичные репозитарии.
*outputs.tf* - описание выходных переменных. Необязательный файл, но очень удобно выделять нужные параметры из созданного инстанса, например IP созданного в облаке инстанса.

</details>
