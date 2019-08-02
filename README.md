# GyverCore for ATmega328/168
 Быстрое и лёгкое ядро для Arduino IDE.
 Основано на оригинальном ядре Arduino версии 1.8.9, большинство функций заменены на более быстрые и лёгкие аналоги, убрано всё лишнее и не относящееся к микроконтроллеру ATmega328p, убран почти весь Wiring-мусор, код упрощён и причёсан. 

## Установка
- Открой the Arduino IDE
- Зайди в **Файл > Настройки**
- Вставь этот адрес в **Дополнительные ссылки для менеджера плат**:
    ```
    https://alexgyver.github.io/package_GyverCore_index.json
    ``` 
- Открой **Инструменты > Плата > Менеджер плат...**
- Подожди загрузку списка
- Листай в самый низ, пока не увидишь **GyverCore**
- Жми **Установка**
- Закрой окно
- Выбери плату в **Инструменты > Плата > GyverCore > ATmega328/168 based**
- Выбери в **CPU & BOOT** нужный вариант загрузчика
- Готово!
- *Примечание*: файлы ядра находятся по пути *C:\Users\Username\AppData\Local\Arduino15\packages\GyverCore\hardware\avr\1.0.0\*

## Изменения
### Облегчено и ускорено
Время выполнения функций, мкс

Функция         | Arduino   | GyverCore | Быстрее в 
----------------|-----------|-----------|----------
pinMode         | 2.90 us   | 0.57 us   | 5.09      
digitalWrite    | 2.90 us   | 0.57 us   | 5.09      
digitalRead     | 2.45 us   | 0.50 us   | 4.90      
analogWrite     | 4.15 us   | 1.13 us   | 3.67      
analogRead      | 112.01 us | 5.41 us   | 20.70     
analogReference | 0.00 us   | 0.69 us   | 0.00      
attachInterrupt | 1.20 us   | 1.18 us   | 1.02      
detachInterrupt | 0.82 us   | 0.57 us   | 1.44    
tone			| 5.63 us   | 2.40 us   | 2.3     

Занимаемое место, Flash, байт

Функция         | Arduino | GyverCore | Разница, Flash 
----------------|---------|-----------|---------------
pinMode         | 114     | 24        | 90             
digitalWrite    | 200     | 24        | 176            
digitalRead     | 190     | 24        | 166            
analogWrite     | 406     | 48        | 358            
analogRead      | 32      | 72        | -40            
analogReference | 0       | 32        | -32            
attachInterrupt | 212     | 180       | 32             
detachInterrupt | 198     | 150       | 48         
tone      		| 1410    | 740       | 670       
Serial.begin    | 1028    | 166       | 862            
print long      | 1094    | 326       | 768            
print string    | 2100    | 1484      | 616            
print float     | 2021    | 446       | 1575           
parseInt        | 1030    | 214       | 816            
readString      | 2334    | 1594      | 740            
parseFloat      | 1070    | 246       | 824         

Примечание: analogRead и analogReference имеют расширенную функциональность и весят чуть больше  
Скетч, состоящий из однократного вызова перечисленных выше функций, занимает 
- Ядро Arduino: 3446 байт (11%) Flash / 217 байт (10%) SRAM
- Ядро GyverCore: 1436 байт (4%) Flash / 94 байт (4%) SRAM

### Добавлено
- Подсветка в коде **A0**, **A1**.. **A7**
- Макрос **bitToggle**(value, bit), инвертирует состояние бита bit в байте value
- Функция **digitalToggle**(pin), инвертирует состояние пина
- Расширенная генерация ШИМ:	
	- **setPWM_20kHz**(byte pin) - установить частоту ШИМ 20 кГц (8 бит) **на пинах 3, 5, 9, 10**
	- **setPWM_9_10_resolution**(boolean resolution) - разрешение ШИМ **на пинах 9 и 10** (для режима 20 кГц): **PWM_8BIT** / **PWM_10BIT**
	- **setPwmFreqnuency**(pin, freq) - установить частоту ШИМ (8 бит) **на пинах 3, 5, 6, 9, 10, 11**: **PWM_DEFAULT** / **PWM_8KHZ** / **PWM_31KHZ**
	- **setPWM_default**(byte pin) - настроить ШИМ по умолчанию
- Расширенная работа с АЦП
	- **analogStartConvert**(byte pin) - начать преобразование с выбранного пина
	- **analogGet()** - получить преобразованное значение (между analogStartConvert и analogGet можно выполнять действия, в отличие от ожидания в analogRead())
	- **analogPrescaler**(uint8_t prescl) - установить предделитель для АЦП (2, 4, 8, 16, 32, 64, 128)
	- В функции **analogRead(pin)** вместо пина можно указать **INTERNAL** (получить значение внутреннего опорного напряжения) или **THERMOMETR** (получить приблизительную температуру МК). *Примечание: нужно установить предделитель 128*
- Добавлен быстрый и лёгкий UART (аналог классу Serial)
	- **uartBegin()** - запустить соединение по последовательному порту со скоростью 9600
	- **uartBegin(baudrate)** - запустить соединение по последовательному порту со скоростью baudrate
	- **uartEnd()** - выключить сериал
	- **uartPeek()** - вернуть крайний байт из буфера, не убирая его оттуда
	- **uartClear()** - очистить буфер
	- **uartRead()** - вернуть крайний байт из буфера, убрав его оттуда
	- **uartWrite(val)** - запись в порт
	- **uartPrint(val)** - печать в порт (числа, строки, char array)
	- **uartPrintln(val)** - печать в порт с переводом строки
	- **uartAvailable()** - возвразает true, если в буфере что-то есть
	- **uartSetTimeout(val)** - установить таймаут для функций парсинга (по умолчанию 100 мс)
	- **uartParseInt()** - принять целочисленное число
	- **uartReadString()** - принять строку
	- **uartParseFloat()** - принять число float
	- **uartParsePacket(dataArray)** - принять пакет вида **$50 60 70;** в массив dataArray (смотри пример)
	
### Убарно
Убраны всякие сервисные файлы и прочий хлам, не относящийся к ATmega328 (wifi, USB). Ядро полностью совместимо с остальными библиотеками, ничего из стандартных функций не вырезано.
	
## Дополнительно
### Настройки прошивки
- Смотри примеры в папке examples (в корне этого репозитория!)
- Добавлен вариант прошивки без загрузчика **ATmega 328/168 without bootloader** (во всю доступную Flash память) для прошивки через ISP
- Добавлен вариант прошивки с отключенными функциями времени **with/without millis** (освобождает вектор **TIMER0_OVF_vect** для личного пользования)

## Больше контроля!
Для большего контроля за периферией микроконтроллера рекомендую попробовать следующие наши библиотеки:
- **directTimers** - полный контроль над таймерами/счётчиками ATmega328
- **directADC** - полный контроль над АЦП и компаратором ATmega328
- **GyverPWM** - расширенная генерация ШИМ сигнала со всеми настройками и режимами
- **GyverTimer012** - очень простая и лёгкая библиотека для контроля прерываний по таймерам 0/1/2

Скачать все библиотеки можно из [репозитория](https://github.com/AlexGyver/GyverLibs)