# Лабораторная работа 3

## Код программы
**==================================================================================**

using System;
using System.Collections.Generic;	
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Runtime.InteropServices;

class Program
{
    //системный вызов
    [DllImport("kernel32.dll")]
    static extern IntPtr CreateFile(string lpFileName, uint dwDesiredAccess,
        uint dwShareMode, IntPtr lpSecurityAttributes, uint dwCreationDisposition,
        uint dwFlagsAndAttributes, IntPtr hTemplateFile);

    static void Main()
    {
        //консоль
        Console.WriteLine("\n===============================================");
        Console.WriteLine("Лабораторная работа №3");
        Console.WriteLine("Выполнили: Алиев Садык, Дзюба Данил");
        Console.WriteLine("===============================================\n");
        //системный вызов
        string testFile = Path.Combine(Environment.GetFolderPath(
            Environment.SpecialFolder.MyDocuments), "test.txt");

        IntPtr fileHandle = CreateFile(testFile, 0x40000000, 0,
            IntPtr.Zero, 2, 0x80, IntPtr.Zero);

        Console.WriteLine(fileHandle != (IntPtr)(-1) ?
            "Системный вызов CreateFile выполнен" :
            "Ошибка системного вызова");

        // Вывод файлов средствами .NET
        string docsPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
        Console.WriteLine($"\nСодержимое {docsPath}:");

        foreach (var dir in Directory.GetDirectories(docsPath))
            Console.WriteLine($"{Path.GetFileName(dir)}");

        foreach (var file in Directory.GetFiles(docsPath))
            Console.WriteLine($"{Path.GetFileName(file)}");
        
        Console.ReadKey();

    }
}

**==================================================================================**
![код программы](https://github.com/Corleone-Inc/ZIVPO_labs/blob/main/lab3/images/Снимок%20экрана%202025-11-16%20204532.jpg)
<img src="https://github.com/Corleone-Inc/ZIVPO_labs/blob/main/lab3/images/Снимок%20экрана%202025-11-16%20204532.jpg" width="800" height="600">

## Описание работы программы
Программа запускает консольное окно и выводит информацию о лабораторной работе и фамилии выполнивших ее студентов. Затем программа выполняет системный вызов Windows API - создает временный файл test.txt в папке Documents текущего пользователя с помощью функции CreateFile из kernel32.dll. После этого программа получает путь к папке Documents, считывает все подкаталоги и файлы в этой папке и выводит их список в консоль. В конце программа ожидает нажатия любой клавиши пользователем, чтобы консольное окно не закрывалось сразу после выполнения всех операций.

## Ход Работы

**Шаг 1**
Для начала превратили проект в exe-файл. И дизассемблировали его через IDA
Видим, что все функции и программы легко прочитать и понять
!
**Шаг 2**
**Шаг 3**
**Шаг 4**
