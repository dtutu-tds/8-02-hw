# Домашнее задание  - GitLab. Тутубалин Дмитрий

### Задание 1

**Что нужно сделать:**

1. Разверните GitLab локально, используя Vagrantfile и инструкцию, описанные в [этом репозитории](https://github.com/netology-code/sdvps-materials/tree/main/gitlab).   
2. Создайте новый проект и пустой репозиторий в нём.
3. Зарегистрируйте gitlab-runner для этого проекта и запустите его в режиме Docker. Раннер можно регистрировать и запускать на той же виртуальной машине, на которой запущен GitLab.

В качестве ответа в репозиторий шаблона с решением добавьте скриншоты с настройками раннера в проекте.

Ответ: 
Развернул GitLab, прикрутил также https, это уже для удобной работы. Штука интересная, буду юзать обязательно)
 ![WEB](https://github.com/dtutu-tds/8-02-hw/blob/main/screenshot/1%23Web_GitLab.png)

---

### Задание 2

**Что нужно сделать:**

1. Запушьте [репозиторий](https://github.com/netology-code/sdvps-materials/tree/main/gitlab) на GitLab, изменив origin. Это изучалось на занятии по Git.
2. Создайте .gitlab-ci.yml, описав в нём все необходимые, на ваш взгляд, этапы.

В качестве ответа в шаблон с решением добавьте: 
   
 * файл gitlab-ci.yml для своего проекта или вставьте код в соответствующее поле в шаблоне; 
 * скриншоты с успешно собранными сборками.
 
 Ответ: 
 Запушил репозиторий командой - git push -uf origin --all ;
 Предварительно изменил URL на свой - git remote set-url origin https://gitlab.office-dtutu.ru/tds/8-02-hw.git .
 ![PUSH](https://github.com/dtutu-tds/8-02-hw/blob/main/screenshot/2%23push.png)

 Далее я установил gitlab-runner на хост, и зарегестрировал его командой: 
 sudo gitlab-runner register \
  --url "https://gitlab.office-dtutu.ru" \
  --registration-token "***" \
  --executor "docker" \
  --description "local-docker-runner" \
  --tag-list "docker,go" \
  --docker-image "docker:25.0.0" \
  --docker-volumes "/var/run/docker.sock:/var/run/docker.sock"
  ![RUNNER](https://github.com/dtutu-tds/8-02-hw/blob/main/screenshot/2%23runner.png)
  ![PIPELINE_RUN](https://github.com/dtutu-tds/8-02-hw/blob/main/screenshot/pipeline_running.png)
  ![pipeline_passed](https://github.com/dtutu-tds/8-02-hw/blob/main/screenshot/pipeline_passed.png)

Создал файл .gitlab-ci.yml, с таким содержанием:
[image: golang:1.22-alpine           

stages:                             
  - test
  - build

variables:                         
  CGO_ENABLED: "0"

before_script:                    
  - apk add --no-cache git
  - go version

unit_tests:
  stage: test
  script:
    - go test -v ./...               
  artifacts:                        
    when: always
    reports:
      junit: report.xml
    paths:
      - report.xml
  tags: [docker, go]               

build_binary:
  stage: build
  needs: []                          
  script:
    - go build -o myapp .
  artifacts:
    paths:
      - myapp
  tags: [docker, go]
]
---
