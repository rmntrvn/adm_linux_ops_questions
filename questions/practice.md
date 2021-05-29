## Тестовые практические задания

1. Необходимо проходить по списку URL'ов и проверять их доступность. Условия:
   - Список URL'ов находится в файле /urls.txt;
   - Доступный URL - значит код ответа не 5XX или 4XX;
   - Проверка должна быть оформлена в виде функции bash, которая должна вызываться внутри скрипта;
   - Функция должна принимать в качестве входного параметра путь к файлу с URL'ами;
   - При любом ответе недоступности от сервиса - прерывать дальнейшую проверку.
Временное ограничение 20 мин.

<details>
  <summary>Ответ</summary>

Скрипт проверки. Запускать `./script.sh <путь до файла с URLs>`
```bash
#!/usr/bin/env bash

set -xueo pipefail

FILE_URLS=${1:-}
if [[ -z "${FILE_URLS}" ]]; then
  echo "File with URLs list do not defined."
  exit 1
fi

function checkUrls() {
  local URLS=$1
  for URL in $(cat $URLS); do
    STATUS=`curl -LI "${URL}" -o /dev/null -w '%{http_code}' -s`
    if [[ "${STATUS}" == "500" ]] || [[ "${STATUS}" == "400" ]]; then
      echo "URL ${URL} unavailable!"
      exit 1
    else
      echo "URL ${URL} available."
    fi
  done
}

checkUrls "${FILE_URLS}"
```

</details>

2. Есть две изолированные сети /25 - 192.168.1.0 (gw: 192.168.1.1), 192.168.1.128 (gw: 192.168.1.129).
Есть два сервера со следующими таблицами маршрутизации
```
192.168.1.3
routes
0.0.0.0/0 192.168.1.1

192.168.1.146
routes
192.168.1.128/24 192.168.1.129
```
Что нужно сделать, чтобы эти сервера "видели" друг друга?

3. В конфиге nginx некоторого проекта есть два десятка различных location, которые делятся на три базовых типа - memcache, dynamic, static. Лог проекта единый, но для анализа требуется различать записи в логе каким-либо способом. По именам файлов тип location различить нельзя, разделить на три лога также нельзя. Предложите решение.

<details>
  <summary>Ответ</summary>
Использовать вывод в syslog и определить tag.
Например:
	
```
location /memcache {
  access_log syslog:server=unix:/dev/log,tag=nginx_memcache;
  error_log syslog:server=unix:/dev/log,tag=nginx_memcache;
}

location /dynamic {
  access_log syslog:server=unix:/dev/log,tag=nginx_dynamic;
  error_log syslog:server=unix:/dev/log,tag=nginx_dynamic;
}
```

/static - соответственно. Вывод в определенный файл syslog можно указать опцией `:syslogtag`
</details>
