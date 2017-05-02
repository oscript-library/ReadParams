# ReadParams
Чтение параметров из json файла

## Чтение из файла

Читает параметры из файла json и возвращает соответствие параметров. 
Параметры читаются рекурсивно, т.е. если в файле указана структура, в ней структура, а уже в ней п1 = 100, то в соответствии будет прочитанныеПараметры["п1"] = 100

### Пример из тестов 1. Чтение файла

```bsl
записьТекста = Новый ЗаписьТекста(файлПараметров);
ЗаписьТекста.ЗаписатьСтроку( "{""парам.Число"": 100, ""парам.Строка"": ""100"", ""парам.Булево"": true}" );
ЗаписьТекста.Закрыть();

ошибкиЧтения = Неопределено;

прочитанныеПараметры = ЧтениеПараметров.Прочитать( файлПараметров, ошибкиЧтения );

// ТипЗнч(прочитанныеПараметры) = Тип("Соответствие");
// прочитанныеПараметры.Количество() = 3;

// ТипЗнч(ошибкиЧтения) = Тип("Соответствие");
// ошибкиЧтения.Количество() = 0;

// прочитанныеПараметры["парам.Число"] = 100;
// прочитанныеПараметры["парам.Строка"] = "100";
// прочитанныеПараметры["парам.Булево"] = Истина;
	
```
Так же на вход можно подавать файл (Новый файл()) массив файлов, массив путей к файлам.

Если ничего не указывать в качестве пути, то будет прочитан файл **param_os.json** из текущего каталога.

### Пример из тестов 2. Чтение файла из текущего каталога

```
файлПараметров = ОбъединитьПути( ТекущийКаталог(), "param_os.json");

записьТекста = Новый ЗаписьТекста(файлПараметров);
ЗаписьТекста.ЗаписатьСтроку( "{""парам.Число"": 100, ""парам.Строка"": ""100"", ""парам.Булево"": true}" );
ЗаписьТекста.Закрыть();

прочитанныеПараметры = ЧтениеПараметров.Прочитать();

```

## Ссылки в переданном файле на другие файлы к чтению

Если указать в файле параметров имя параметра, которое начинается с **ReadFile**, а в значении путь к файлу (в том числе относительно этого файла), то будут прочитан так же и указанный файл.

### Пример из теста 3. Чтение файла с ссылками на другие файлы

```bsl
путь2 = ПутьВЗначениеJSON( ОбъединитьПути(".", "testParam2.json") );
путь3 = ПутьВЗначениеJSON( ОбъединитьПути(".", "testParam3.json") );

записьТекста = Новый ЗаписьТекста(файлПараметров1);
ЗаписьТекста.ЗаписатьСтроку( "{""парам.Число"": 1, ""парам.Строка"": ""1"", ""парам.Булево"": true, ""ReadFile"": """ + путь2 + """}" );
ЗаписьТекста.Закрыть();

записьТекста = Новый ЗаписьТекста(файлПараметров2);
ЗаписьТекста.ЗаписатьСтроку( "{""парам.Число2"": 2, ""парам.Строка2"": ""2"", ""парам.Булево2"": false, ""ReadFile"": """ + путь3 + """}" );
ЗаписьТекста.Закрыть();

записьТекста = Новый ЗаписьТекста(файлПараметров3);
ЗаписьТекста.ЗаписатьСтроку( "{""парам.Число"": 3, ""парам.Строка"": ""3""}" );
ЗаписьТекста.Закрыть();

прочитанныеПараметры = ЧтениеПараметров.Прочитать( файлПараметров1 );

// прочитанныеПараметры["парам.Число"] = 3;
// прочитанныеПараметры["парам.Строка"] = "3";
// прочитанныеПараметры["парам.Булево"] = Истина;

// прочитанныеПараметры["парам.Число2"] = 2;
// прочитанныеПараметры["парам.Строка2"] = "2";
// прочитанныеПараметры["парам.Булево2"] = Ложь;

// прочитанныеПараметры["ReadFile"] = ОбъединитьПути(".", "testParam3.json");
```

## Подстановки в параметрах

Если для какого-либо параметра в качестве значения используется шаблон %ИмяПараметра%, то в прочитанных параметрах будет предпринята попытка подменить этот шаблон на соответствующее значение

### Пример из тестов 4. Использование подстановки

```bsl
записьТекста = Новый ЗаписьТекста(файлПараметров);
ЗаписьТекста.ЗаписатьСтроку( "{""парам.Число"": 100,
	|""парам.Подстановка1"": ""999+%парам.Число%+%парам.Число%"",
	|""парам.Подстановка2"": ""0+%парам.Подстановка1%+%парам.Число%""
	|}" );
ЗаписьТекста.Закрыть();

прочитанныеПараметры = ЧтениеПараметров.Прочитать( файлПараметров );
	
// прочитанныеПараметры["парам.Число"] = 100;
// прочитанныеПараметры["парам.Подстановка1"] = "999+100+100";
// прочитанныеПараметры["парам.Подстановка2"]  "0+999+100+100+100";	
```

## Обработка ошибок

Модуль не генерирует исключений и не выводит никаких сообщений. Если переданный файл не удалось прочитать или распарсить, то ошибка об этом вернется вторым параметром **ошибкиЧтения**. Это соответствие, где ключ - путь к файлу, значение - описание ошибки.

# Примеры использования

[ОСкрипты для деплоя и копирования базы данных](http://infostart.ru/public/617478/) и они [же на github](http://infostart.ru/redirect.php?url=aHR0cHM6Ly9naXRodWIuY29tL1N0ZXBhODYvMUMtRGVwbG95LWFuZC1Db3B5REI=)

