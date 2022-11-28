# Лабораторная №1

В качестве цели фазинга был выбран инструмент `imglab`.

`imglab` - инструмент для маркировки изображений, который можно использовать для обучения `dlib` или других детекторов объектов. Данная утилита очень востребованна и имеет поддержку большого количества плагинов и расширений. [Ссылка на исходники](https://github.com/davisking/dlib/tree/master/tools/imglab).

## Ход работы
Для начала был скачан VirtualBox, настроена и запущена ВМ на `Ubuntu 22 lts`. Далее были установлены и обновлены нужные пакеты (ну и конечно же решены конфликты).

Далее установлена AFL++ [ссылка на репозиторий](https://github.com/AFLplusplus/AFLplusplus) ([инструкция по установке](https://github.com/AFLplusplus/AFLplusplus/blob/stable/docs/INSTALL.md)).

Перед выполнением проверили, что `afl` установлен и работает (`/usr/local/bin`) и, опционально, попал в `$PATH`
```shell
# Пусть путь к рабочей директории лабораторной будет ~/Workdir/
# Для начала необходимо получить исходный код инструмента.
git clone https://github.com/davisking/dlib ./dlib
cd dlib/tools/imglab

# Добавим отдельную папку для будущей работы
mkdir build; cd build

# Сгенерируем и соберем
cmake -DCMAKE_C_COMPILER=afl-clang -DCMAKE_CXX_COMPILER=afl-clang++ # Можно добавить флагов, но я не смог разобраться (я просто руби разраб)
make # (можно добавить `-j n`, где n -число воркеров, однако на виртуальной убунте иногда всплывают проблемы)

# Создадим отдельные папки для инпута и вывода afl. Наполним входные данные тест кейсами
mkdir -p fuzz/in fuzz/out

# Тестовые данные можно взять, н.п у самого dlib
cp ~/Workdir/dlib/examples/<тестовые файлы> ./fuzz/in

# Запустим AFL++ на 2 часа (из папки билда)
afl-fuzz -i fuzz/image/in -o fuzz/image/out -- ./imglab @@
```

## Результат работы
Скриншот проработавшей утилиты в течении > 2 часов.
![image](https://user-images.githubusercontent.com/90094191/204144593-963142f8-5881-439c-bd7c-1a1f1a0db8ef.png)

Вывод программы лежит в репозитории (Добавил все, что попало в `fuzz/out/default`)

P.S. из странностей `CPU usage` на хостовой машине не поднимался выше 20%, но afl иногда умудрялся выводить 300-700% использования (виртуалке выделил половину).
