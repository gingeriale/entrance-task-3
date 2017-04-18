# Задание 3

Мобилизация.Гифки – сервис для поиска гифок в перерывах между занятиями.

Сервис написан с использованием [bem-components](https://ru.bem.info/platform/libs/bem-components/5.0.0/).

Работа избранного в оффлайне реализована с помощью технологии [Service Worker](https://developer.mozilla.org/ru/docs/Web/API/Service_Worker_API/Using_Service_Workers).

Для поиска изображений используется [API сервиса Giphy](https://github.com/Giphy/GiphyAPI).

В браузерах, не поддерживающих сервис-воркеры, приложение так же должно корректно работать, 
за исключением возможности работы в оффлайне.

## Структура проекта

  * `gifs.html` – точка входа
  * `assets` – статические файлы проекта
  * `vendor` –  статические файлы внешних библиотек
  * `service-worker.js` – скрипт сервис-воркера

Открывать `gifs.html` нужно с помощью локального веб-сервера – не как файл. 
Это можно сделать с помощью встроенного в WebStorm/Idea веб-сервера, с помощью простого сервера
из состава PHP или Python. Можно воспользоваться и любым другим способом.


# Решение
> SW - сокращение для service worker

**Ошибка №1**: SW не обрабатывал запросы за статикой.

**Баг**: избранное не работает в режиме оффлайн.
 
**Причина**: SW обрабатывает запросы только файлов, находяшихся в его скоупе, т.е. 
находящихся глубже в каталоге, т.о. в проекте не было ни одного файла, который
смог бы обработать SW.

**Решение**: переместить SW в корень проекта, теперь все файлы, находящиеся
в папках со статикой находятся в скоупе SW, обрабатываются запросы из файлов
скоупа, доступно избранное оффлайн.
***
**Ошибка №2**: Не кешируется точка входа в приложение - `gifs.html`.

**Баг**: не загружается главная страница при отсутствии сети.

**Причина**: отсутствует условие для кеширования файла в функции
`needStoreForOffline()`.

**Решение**: добавить условие для кеширования этой страницы
`cacheKey.includes('gifs.html')`.
***
**Ошибка №3**: приложение берет ресурсы из кеша всегда, а не только
при отсутствии сети.

**Баг**: невозможно получить новую информацию с сервера приложения.

**Причина**: на этапе `fetch` при обращении за ресурсами приложения
выполнялось условие, что если в кеше найден соответствующий адрес, то
получить данные из кеша, и только в случае, если адрес не найден,
выполнить запрос и положить данные в кеш.

**Решение**: убрать излишнее условие, возможно упростить запись -
как исправлено в SW `addEventListener` для события `fetch`.
***
**Дополнительное задание**: возможность перехода в оффлайн после
первого же запроса.

**Решение**: закешируем данные на этапе установки SW - добавим в
`event.waitUntil()` еще один промис, который при установке SW откроет
кеш и положит туда все ресурсы приложения.
***
Под вопросами в комментариях к `service-worker.js` приведены ответы
на них.




