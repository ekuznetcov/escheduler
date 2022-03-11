# EScheduler
Этот репозиторий содержитпроект EScheduler. EScheduler - это система сбора и анализа данных энергопотребления многопроцессорной вычислительной системы (ВС) кластерного типа, состоящая из подсистем сбора данных, подсистемы анализа данных  и подсистемы визуализации данных.
## Подсистема сбора данных

msr_exporter - это программное средство сбора данных об энергопотреблении ЦПУ на ВС с ОС Linux.

**поддерживаемые платформы**

msr_exporter поддерживает все архитектуры процессоров поддерживающие интерфейс RAPL

- Intel начиная с архиетктуры Sandy Bridge
- AMD начиная с архитктуры Pyzen

Для сбора данных msr_exporter использует msr-регистры

Всего существует три способа для чтения информации с доменов RAPL:

- Читать msr-регистры напрямую с /dev/cpu/%d/msr
- Через perf_event_open() interface (не поддерживается AMD)
- Через sysfs powercap (не поддерживается AMD)

Для получения данных с powercup или sysfs используйте [node_exporter](https://github.com/prometheus/node_exporter)

msr_exporter поддерживает домены RAPL package(PP0, PP1, DRAM), platform(PSys)

msr_exporter (пока не поддерживает AMD рахитектуру по причине. того, что не удалось выяснить какие регистры используются в AMD процессорах)

**💾 Копиляция**

node_exporter написан на языке go

Для компиляции необходимо выполнить 

```
go build main.go
```

Также необходимо убедиться что перееменные GOOS и GOARCH равны linux и amd64 соответственно

**🚀 Начало работы**

Перед началом работы необходимо установить драйвер 

```bash
modprobe msr
```

Также понадобятся root права

Параметры запуска программы задаются в файле msr_exporter.yaml

Всего возможно задать два параметра 

- Address - это адрес на который будут экспортироваться метрики
- Interface - это драйвер, который используется для обращения к msr-регистрам например `/dev/cpu/%d/msr` или `/dev/cpu/%d/msr_safe`

**💡 Systemd сервис**

msr_exporter может быть установлен как сервис.

Для этого необходимо скопировать файл msr_exporter.service  в `/etc/systemd/system` в ExecStart прописать путь до дирректории с исполняемым файлом и файлом конфигурации

## Подсиситема анализа данных
Подсистема анализа данных получает агрегированные данные от Prometheus по HTTP API и записывает их в постгрес, после при следующем запуске этой программы необходимо, если пользователь выбирает запуск задания с учетом энергопотребления, подсистема анализа данных смотрит на какаих ВС уже запускалось данная программа (если на какой-то системе она до этого не была запущена, считается что значение энергопотребления рравным нулю) и выбирает ту систему, на которой за время выполнения задания пиковое значение энергопотребления было наименьшим.

## Подсистема визуализациии данных
Подсистема визуализации данных об энергопотреблении параллельных программ позволяет оценить максимальное, минимальное, среднее и суммарное значение энергопотребления как для одной, так и для нескольких параллельных программ пользователя. Данные об энергопотреблении могут представляются как в текстовом, так и в графическом виде с отображением диаграмм и графиков с помощью программного средства Grafana.
