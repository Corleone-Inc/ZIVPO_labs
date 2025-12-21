# - Лабораторная №1
Для анализа я взял вирус из раздела Samples/Families/ActionSpy от 2025/07/09.
Прогнав файл через IDA Pro, я заметил вызовы типа Environment.getExternalStorageDirectory(), которые отзывали на Java/Android компоненты.
Для более удобного анализа кода я установил jadx (dex to java decompiler) и декомпилировал код там.
Первое, что я проверил, это какие разрешения получает программа в ОС. В файле AndroidManifest.xml были следующие строки:
<uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>

    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>

    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>

    <uses-permission android:name="android.permission.WAKE_LOCK"/>

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
Выходит, вирус получает полный доступ к нужным ему функциям.
Также я увидел следующие подозрительные строчки кода: UploadFileAction.go() / UploadFileAction.onNetworkReturn(...) - файлы загружаюся на какой то сервер. NetOperHelper.doHttpPostFile(java.lang.String, java.lang.String, java.lang.String) и doHttpPost(...) — методы отправки HTTP POST и файлов (multipart). Также используются функции кодировки передаваемых файлов в Base64.
Периодически проверяется состояние соединения с помощью HeartAction. Присутствует возможность подгрузки дополнительных модулей/плагинов злоумышленником на устройство с помощью PluginManager. Сбор телеметрии Utils.getProcFileInfo(String, String).
Также, возможно, злоумышленник находится в Китае, или вирус предназачен для китайских устройств, я если честно не понял, но вот строчка кода, которая указывает на региональные настройки Китая:
public static final DateFormat DATE_FORMAT = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss", Locale.CHINA);
В общем, можно построить такую модель поведения вредоносной программы:
1. Постоянность и автостарт

Есть сервис MyService, возможно планировщик (AlarmManager) — агент стартует в фоне и периодически выполняет задачи.

2. Телеметрия / Heartbeat

HeartAction формирует отчёт/heartbeat (включая deviceid, userId, возможно /proc-инфо) и отправляет на сервер. Ответы обрабатываются и декодируются (возможно с проверкой подписи RSA).

3. Экфильтрация данных / файловая отправка

UploadFileAction читает файлы (включая внешнюю память), кодирует (Base64) и отправляет через multipart POST (doHttpPostFile). Это основной вектор утечки данных.

4. Криптография и контроль целостности

Использование RSA (public key в AppEnv) говорит о проверке подлинности команд или защите конфигурации (сервер подписывает команды).

5. Модульность / обновляемость

PluginManager и возможность подгрузки кода (рефлексия / DexClassLoader — явные индикаторы, если найдутся) позволяют оператору расширять функционал на лету.

6. Сетевая логика/режимы

ConnectModeManager указывает на смену способов соединения (HTTP, прокси и т.п.) в зависимости от условий/команд.
Ну и учитывая с какого раздела я скачал вирус, очевидно, что это шпион, который отправляет личные данные на удаленный сервер, а также имеет возможность подгружать новые модули для расширения своего функционала.
