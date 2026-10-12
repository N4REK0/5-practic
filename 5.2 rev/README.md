# Практическая работа №5
## Выполнили студенты группы П25-2.1. Оганджанян Нарек Артёмикович и Хохлов Максим
## Тема: Колонизация Марса

<picture> <img src="скрины 5 2 rev/1.png"> 
</picture>

<picture> <img src="скрины 5 2 rev/2.png"> 
</picture>

<picture> <img src="скрины 5 2 rev/3.png"> 
</picture>

``` csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace _5_practic_2_rev
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("=== КОЛОНИЗАЦИЯ МАРСА: ИНЖЕНЕР БАЗЫ ===");
            Console.WriteLine("На станции произошли три аварии!");
            Console.WriteLine("Устраните поломки, пока давление не упало до нуля.\n");

            // 1. Массив инструментов на 5 слотов.
            string[] tools = new string[5]
            {
            "Скафандр",
            "Мультитул",
            "Герметик",
            "Кислородный баллон",
            "Запасной чип"
            };

            // 2. Синхронные массивы аварийных модулей.
            string[] modules = new string[3]
            {
            "Шлюз — разгерметизация",
            "Внешний отсек — пылевой смерч",
            "Реакторный отсек — сбой реактора"
            };

            int[] criticality = new int[3] { 40, 75, 140 };
            int[] pressureDrop = new int[3] { 3, 5, 8 };

            // 3. Радиация в 6 точках.
            int[] radiationLevels = new int[6] { 12, 28, 15, 42, 19, 33 };

            // 6. Время работ в каждом модуле заполняется во время игры.
            int[] repairTimes = new int[3];

            Random randomGenerator = new Random();

            int maxPressure = 100;
            int currentPressure = maxPressure;
            int repairedModules = 0;
            bool stationLost = false;

            Console.WriteLine("Инструменты инженера:");

            foreach (string tool in tools)
            {
                Console.WriteLine("- " + tool);
            }

            // 4. Вывод параметров аварийных модулей через for.
            Console.WriteLine("\nАварийные модули:");

            for (int i = 0; i < modules.Length; i++)
            {
                Console.WriteLine($"{i + 1}. {modules[i]} - {criticality[i]}, критичность: {criticality[i]} HP, падение давления: {pressureDrop[i]} кПа/мин");
            }

            // --- ИСПОЛЬЗОВАНИЕ ГЕРМЕТИКА ---

            // 5. Поиск герметика и замена на "Использовано".
            Console.WriteLine("\nПеред входом на станцию нужно заделать трещину шлюза.");

            int sealantIndex = Array.IndexOf(tools, "Герметик");

            if (sealantIndex != -1)
            {
                tools[sealantIndex] = "Использовано";
                criticality[0] -= 15;

                Console.WriteLine("Герметик найден и использован.");
                Console.WriteLine("Критичность аварии шлюза уменьшена на 15 HP.");
            }
            else
            {
                Console.WriteLine("Герметика нет. Ремонт шлюза будет сложнее.");
            }

            // --- ИНТЕРАКТИВНЫЙ ЦИКЛ РЕМОНТА ---

            for (int i = 0; i < modules.Length; i++)
            {
                Console.WriteLine($"\n=== ЭТАП {i + 1}: {modules[i]} ===");

                // Работаем с текущей аварией до её устранения
                // или пока давление не станет равно нулю.
                while (criticality[i] > 0 && currentPressure > 0)
                {
                    Console.WriteLine(
                        $"\nДавление станции: {currentPressure}/{maxPressure} кПа");

                    Console.WriteLine(
                        $"Оставшаяся критичность аварии: {criticality[i]} HP");

                    Console.WriteLine("Выберите действие:");
                    Console.WriteLine("1. Ремонт мультитулом: 20–35 HP, 2 минуты");
                    Console.WriteLine("2. Использовать запасной чип: 70 HP, 1 минута");
                    Console.WriteLine("3. Использовать кислородный баллон: +50 кПа, 1 минута");
                    Console.Write("Ваш выбор (1–3): ");

                    string choice = Console.ReadLine();

                    if (choice == null)
                    {
                        Console.WriteLine("Ввод завершён. Игра остановлена.");
                        return;
                    }

                    int repairPower = 0;
                    int actionTime = 0;

                    if (choice == "1")
                    {
                        repairPower = randomGenerator.Next(20, 36);
                        actionTime = 2;

                        Console.WriteLine(
                            $"Вы ремонтируете модуль мультитулом: {repairPower} HP.");
                    }
                    else if (choice == "2")
                    {
                        int chipIndex = Array.IndexOf(tools, "Запасной чип");

                        if (chipIndex == -1)
                        {
                            Console.WriteLine("Запасной чип уже использован!");
                            continue;
                        }

                        tools[chipIndex] = "Использовано";
                        repairPower = 70;
                        actionTime = 1;

                        Console.WriteLine("Чип установлен в систему аварийного управления.");
                        Console.WriteLine("Критичность аварии уменьшается на 70 HP.");
                    }
                    else if (choice == "3")
                    {
                        int oxygenIndex = Array.IndexOf(tools, "Кислородный баллон");

                        if (oxygenIndex == -1)
                        {
                            Console.WriteLine("Кислородный баллон уже использован!");
                            continue;
                        }

                        if (currentPressure >= maxPressure)
                        {
                            Console.WriteLine("Давление полное. Баллон пока не нужен.");
                            continue;
                        }

                        int restoredPressure = maxPressure - currentPressure;

                        if (restoredPressure > 50)
                        {
                            restoredPressure = 50;
                        }

                        currentPressure += restoredPressure;
                        tools[oxygenIndex] = "Использовано";
                        actionTime = 1;

                        Console.WriteLine(
                            $"Баллон подключён: давление повышено на {restoredPressure} кПа.");
                    }
                    else
                    {
                        Console.WriteLine("Неверный выбор! Введите 1, 2 или 3.");
                        continue;
                    }

                    // Уменьшаем критичность текущей аварии.
                    criticality[i] -= repairPower;

                    if (criticality[i] < 0)
                    {
                        criticality[i] = 0;
                    }

                    // 6. Учитываем время всех работ в текущем модуле.
                    repairTimes[i] += actionTime;

                    // Пока выполнялось действие, давление продолжало падать.
                    int pressureLoss = pressureDrop[i] * actionTime;
                    currentPressure -= pressureLoss;

                    if (currentPressure < 0)
                    {
                        currentPressure = 0;
                    }

                    Console.WriteLine(
                        $"Прошло {actionTime} мин. Потеря давления: {pressureLoss} кПа.");

                    if (currentPressure == 0)
                    {
                        stationLost = true;
                        Console.WriteLine("Давление упало до нуля! Работа остановлена.");
                        break;
                    }

                    if (criticality[i] == 0)
                    {
                        Console.WriteLine("Авария устранена! Модуль восстановлен.");
                    }
                }

                if (stationLost)
                {
                    break;
                }

                repairedModules++;

                Console.WriteLine(
                    $"Время восстановления модуля: {repairTimes[i]} мин.");
            }

            // --- ИТОГ ИГРЫ ---

            if (stationLost)
            {
                Console.WriteLine("\n=== ПОРАЖЕНИЕ ===");
                Console.WriteLine("Станция потеряла давление.");
                Console.WriteLine("Инженер эвакуирован в скафандре.");
            }
            else
            {
                Console.WriteLine("\n=== ПОБЕДА ===");
                Console.WriteLine("Все три аварии устранены. Станция спасена!");
            }

            Console.WriteLine($"Восстановлено модулей: {repairedModules}/{modules.Length}");

            // --- АНАЛИТИКА ---

            // 7. Суммарное время работ через for.
            int totalRepairTime = 0;

            Console.WriteLine("\nВремя, затраченное на работы:");

            for (int i = 0; i < repairTimes.Length; i++)
            {
                totalRepairTime += repairTimes[i];

                Console.WriteLine($"{modules[i]}: {repairTimes[i]} мин.");
            }

            Console.WriteLine($"Общее время работ: {totalRepairTime} мин.");
            Console.WriteLine("Время включает ремонт и подключение кислородного баллона.");
            Console.WriteLine("При поражении учитываются и незавершённые работы.");

            // 8. Поиск максимальной радиации.
            int maxRadiation = radiationLevels[0];
            int maxPointIndex = 0;

            for (int i = 1; i < radiationLevels.Length; i++)
            {
                if (radiationLevels[i] > maxRadiation)
                {
                    maxRadiation = radiationLevels[i];
                    maxPointIndex = i;
                }
            }

            Console.WriteLine(
                $"\nМаксимальная радиация: {maxRadiation} мкЗв/ч.");
            Console.WriteLine($"Опасная контрольная точка: №{maxPointIndex + 1}.");

            // 9. Сортировка замеров радиации.
            Array.Sort(radiationLevels);

            Console.WriteLine("\nРадиация по возрастанию:");
            foreach (int level in radiationLevels)
            {
                Console.Write($"{level} ");
            }
            Console.WriteLine();

            // 10. Расширение массива инструментов после доставки груза.
            Console.WriteLine("\nС Земли прибыл груз с новым оборудованием.");

            if (stationLost)
            {
                Console.WriteLine("Груз передан инженеру на эвакуационном корабле.");
            }

            Array.Resize(ref tools, 7);
            tools[5] = "Сварочный аппарат";
            tools[6] = "Дозиметр";

            Console.WriteLine($"Теперь в инвентаре {tools.Length} слотов:");

            int slotNumber = 1;

            foreach (string tool in tools)
            {
                Console.WriteLine($"Слот №{slotNumber}: {tool}");
                slotNumber++;
            }

            Console.WriteLine("\nНажмите Enter для выхода.");
            Console.ReadLine();
        }
    }
}

```
---
<picture> <img src="скрины 5 2 rev/4.png"> 
</picture>

<picture> <img src="скрины 5 2 rev/5.png"> 
</picture>

<picture> <img src="скрины 5 2 rev/6.png"> 
</picture>
