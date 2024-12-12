# Лабораторная работа 5
## Задание 1 - Автоматизация проверки формата файлов при коммите

1) Папка .git/hooks уже была создана автоматически, так что я просто создал в ней новый файл pre-commit
  ```
  touch pre-commit
  ```
2) Напишем скрипт, который будет проверять наличие подписи DG в конце каждого .txt файла при коммите
```
#!/bin/bash

# Проверяем, что изменяются файлы .txt
txt_files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.txt$')

if [[ -z "$txt_files" ]]; then
  echo "Нет изменений в файлах .txt. Проверка пропущена."
  exit 0
fi

# Проверяем каждый .txt файл
result=0
for file in $txt_files; do
  # Проверка на наличие хотя бы одной строки
  if [[ $(wc -l < "$file") -eq 0 ]]; then
    echo "Ошибка: Файл '$file' пуст!"
    result=1
  # Проверка на наличие подписи "DG" в конце файла
  elif [[ "$(tail -n 1 "$file")" != "DG" ]]; then
    echo "Ошибка: В файле '$file' отсутствует подпись \"DG\" в конце файла!"
    result=1
  else
    echo "Файл '$file' пройден."
  fi
done

# Вывод результата проверки
if [[ $result -eq 0 ]]; then
  echo "Проверка формата файлов .txt пройдена успешно."
  exit 0
else
  echo "Проверка формата файлов .txt не пройдена. Коммит отменен."
  exit 1
fi
```
3) Протестируем работу скрипта. Для этого создадим .txt файл без подписи и с ней
   ![image](https://github.com/user-attachments/assets/c7b1f17e-6f7f-4b0c-867f-142b35e1f2a3)
   ![image](https://github.com/user-attachments/assets/050b50ea-34ad-4c6f-a985-4878713d4dbb)
   ![image](https://github.com/user-attachments/assets/4947fa83-0df9-48cf-988e-323fa1e32df7)

## Задание 2 - Использование Git Flow в проекте
1) Так как Git Flow у меня уже был установлен, то я просто иниицировал его в своем репозитории

```
git flow init
```

2) Создал ветку для новой функциональности, назвав ее "task-management":

```
git flow feature start task-management
```

3) Создал новый файл task_manager.py и записал туда следующую функцию:

```
def create_task(title, description):
    # Логика создания задачи
    print(f"Создана новая задача: {title}")
```

4) Выполнил коммит изменений

```
git add task_manager.py
git commit -m "Добавлен функционал управления задачами"
```

5) После завершения разработки функции завершил фичу и объединил ее с основной веткой:

```
git flow feature finish task-management

```

Git Flow автоматически переключился на ветку develop и выполнил слияние.
![image](https://github.com/user-attachments/assets/e6feefdf-bf68-4d20-8f28-32e9d9cd41f0)

6) Начал создание нового релиза:

```
git flow release start v1.0.0
```

7) Внес изменения, связанные с релизом:

```
echo "v1.0.0" > version.txt
git add version.txt
git commit -m "Обновлена версия для релиза v1.0.0"

```

8) Завершил релиз и объединил его с ветками "develop" и "main":

```
git flow release finish v1.0.0
```

9) Создал hotfix:

```
git flow hotfix start hotfix-1.0.1
```

10) Закоммитил изменения:

```
git add file_with_error.py
git commit -m "Исправлена критическая ошибка"
```

11) Завершил hotfix и объединил его с ветками "develop" и "main":

```
git flow hotfix finish hotfix-1.0.1
```

12. Завершил работу и отправил изменения на удаленный репозиторий:

```
git push origin develop
git push origin main
```
