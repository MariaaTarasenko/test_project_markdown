# HELM chart с шаблонами для ACR PDP sidecar

Содержит шаблоны для конфигурации backend сервисов с Open Policy Agent (OPA) в виде PDP sidecar.

## Настройка зависимости HELM-CHART

Подключить зависимость в файле `Chart.yaml`:

```yaml
dependencies:
  - name: acr-pdp
    version: ^1.0.0-0
    repository: "@dbp"
```

## Настройка содержимого `values.yaml`

```yaml
pdpSidecar:
    policiesFilePath: "files/policies/**"
```
Параметр `policiesFilePath` — путь к файлам с политиками для OPA (формат: `*.rego`, `*.json`). 
Остальные параметры (порт сервиса, параметры docker контейнера, таймауты и др.) настроены по умолчанию и их можно изменить при необходимости.

## Настройка шаблона `ConfigMap`

Нужно создать отдельный шаблон `ConfigMap` со следующим содержимым:

```yaml
{{- include "acr-pdp.configmap" . -}}
```
## Настройка шаблона `Service`

Нужно вставить следующий код в шаблон Service  (раздел с массивом портов):

```yaml
{{- include "acr-pdp.sidecar.port" . | nindent 4}}
```
## Настройка контейнеров

1. Нужно добавить аннотации в шаблон будущих POD (например, в `Deployment/StatefulSet/др.)` в раздел `metadata`.)

```yaml
spec:
  template:
    metadata:
      annotations:
        {{- include "acr-pdp.sidecar.label-logging" . | nindent 8 }}
```
2. Нужно добавить следующий код в шаблон монтируемых контейнеров (например, в `Deployment/StatefulSet/др` в раздел `containers`)

```yaml
containers:
    {{- acr-pdp.sidecar.container" . | nindent 8 }}
```    