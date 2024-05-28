Примеры кода в main.ipynb. Текст:   

# Вступление
>__Талантливый школьник во время сезона 2024 нарисовал новый логотип Красноярской Летней Школы. Оно так всем понравилось, что дирекция решила отсканировать это изображение и сохранить его на компьюетере. Получился файл формата BMP с расширением ".bmp"__

Эту картинку можно найти по ссылке (?):   

<img src="logo.bmp" width="200" height="200">

Известно, что байтовое представление .bmp изображения имеет следующую структуру :
1. BITMAPFILEHEADER - первые 14 байт
1. BITMAPINFO - в наших задачах это всегда будут следующие 40 байт
1. Пиксельные данные - всё остальное

Подробную информацию про формат можно прочитать на [википедии](https://ru.wikipedia.org/wiki/BMP#Таблица_цветов), но из всех этих данных нас будут интересовать в основном следующее:
1. с 0A по 0С (10-14) байт запсиано положение пиксельных данных относительно начала файла
1. с 12 по 16 (18-22) байт записана ширина изображения в пикселях
1. с 16 по 2A (22-26) байт записана высота изображения в пикселях. Если это число положительное, то строки пикселеей записаны снизу вверх. Если это число отрицательное, то запись строк идёт сверху вниз, причём количество строк равно абсолютному значению этого числа
1. с 1С по 1E (28-30) байт записано количество бит, которым шифруется каждый пиксель. В нашем случае - 24 бита = 3 байта

Пиксельные данные записаны последовательностью цветов пикселей по следующим правилам:
- Всё изображение разбито на однопиксельные строки, которые идут по порядку
- Каждый пиксель зашифрован тремя числами от 0 до 255 в палитре rgb - то есть значениями, красной, зелёной и синей компонент цвета пикселя. 
- Пиксели в каждой строке записаны строго слева направо. 
- В конце каждой строки стоит один байт "00" - конец строки.
- Порядок строк зависит от значения высоты, записанной в BITMAPINFO. Отрицательный - сверху вниз, положительный - снизу вверх.

# Задание 1 (Варинат 1):   
## Задание:
Напишите функцию (метод) ```half_image(input_image, result_image)```
, которая заливает верхнюю половину BMP изображения чёрным цветом.    
У этой функции должно быть два аргумета:     
- ```input_image : string``` - относительный или абсолютный путь к BMP изображению описанного выше формата. Известно, что изображение имеет чётное колчиество пикселей по высоте.
- ```result_image : string``` - относительный или абсолютный путь, по которому будет записан результат выполнения функции.   
    
Эта функция (метод) должен использовать только функции стандартных библиотек вашего языка программирования.

# Задание 1 (Варинат 2):   
>__Из-за ошибки при сканировании, изображение получилось перевёрнутым.__         
__Тебя попросили вернуть изображению правильную ориентацию__
## Задание:
Напишите функцию (метод) ```vertical_reverse_image(input_image, result_image)```
, которая изменит порядок строк изображения на противоположный.    
У этой функции должно быть два аргумета:     
- ```input_image : string``` - относительный или абсолютный путь к BMP изображению описанного выше формата.
- ```result_image : string``` - относительный или абсолютный путь, по которому будет записан результат выполнения функции.   
    
Эта функция (метод) должен использовать только функции стандартных библиотек вашего языка программирования.

# Задание 2 (Вариант 1)
>__Дирекция очень довольна твоей работой и захотела отправить это изображение по инетрнету из Орбиты в Красноярск, но интернет в Орибте очень медленный, а наше изображение весит целых 7,5 Мегабайт!__     
__Тебя попросили сжать это изображение так, чтобы при разжатии можно было восстановить, в точности то же самое изображение.__

Давайте заметим, что наше изображение имеет не слишком много различных цветов. Каждый пиксель кодирует 2^24 возможных цветов, хотя иcпользвуется явно меньше 100. Получается, что наш файл хранит излишнюю информацию, и если избавиться от части излишней информации, то мы сможем сократить размер файла.     
## Задание:
1. Напишите две функции ```compress(input_image, result_image)``` и ```decompress(input_image, result_image)```, которые будут сжимать и разжимать обратно файл изображения по заданному алгоритму.
    - ```input_image : string``` - относительный или абсолютный путь к BMP изображению описанного выше формата. Гарантируется, что во входном изображении не более 256 различных цветов из 24-битной rgb палитры.
    - ```result_image : string``` - относительный или абсолютный путь, по которому будет записан результат выполнения функции.   

    Результатом сжатия (функции ```compress()```) должен стать файл формата ".cmp", который получается из оригинального BMP файла по следующему алгоритму:
    1. Первые 54 байта файла .cmp такие же как и файла .bmp
    1. Следующие 256 * 3 байта идёт "таблица использованных цветов". Это последовательность цветов, которые используются в данном изображении. 
    1. Далее идёт пиксельная информация, но каждый пиксель теперь указывается не тремя байтами rgb палитры, а одним байтом - номером позиции, на который этот цвет стоит в "таблице цветов"

    Результатом разжатия (функции ```decompress()```) должно стать BMP изображение, разжатое из файла ".cmp"         
    Эта функции (методы) должны использовать только функции стандартных библиотек вашего языка программирования.
1. Во сколько раз наш метод сжимает такие изображения? А в изображении исползовано больше 256 цветов? 
# Задание 2 (Вариант 2)
>__Дирекция очень довольна твоей работой и захотела отправить это изображение по инетрнету из Орбиты в Красноярск, но интернет в Орибте очень медленный, а наше изображение весит целых 7,5 Мегабайт!__     
__Тебя попросили сжать это изображение так, чтобы при разжатии можно было восстановить, в точности то же самое изображение.__

Давайте посмотрим на верхнюю пиксельную строку нашего изображения. Она же состоит из белого пикселя "ff ff ff" повторенного много раз (столько раз, сколько влезает в одну строку изображения).     
Но!     
Мы же можем сильно сокртатить запись этого изображения, если вместо текущей записи укажем что-то вроде      
**"пиксель ff ff ff стоит 1629 раз"**, или просто **"ff ff ff 1629"**.      
Вспомним теперь, что числа у нас тоже хранятся в байтах, поэтому под число повторов мы выделим один байт. Тогда запись будет выглядеть следующим образом: **"ff ff ff 256, ff ff ff 256, ...,  ff ff ff 93"**.      
Но даже такая запись сильно сократит размер нашего файла, ведь теперь первая строка будет занимать **28 вместо 4887 байт!**  
## Задание:
1. Напишите две функции ```compress(input_image, result_image)``` и ```decompress(input_image, result_image)```, которые будут сжимать и разжимать обратно файл изображения по заданному алгоритму.
    - ```input_image : string``` - относительный или абсолютный путь к BMP изображению описанного выше формата. Гарантируется, что во входном изображении не более 256 различных цветов из 24-битной rgb палитры.
    - ```result_image : string``` - относительный или абсолютный путь, по которому будет записан результат выполнения функции.   

    Результатом сжатия (функции ```compress()```) должен стать файл формата ".cmp", который получается из оригинального BMP файла по следующему алгоритму:
    1. Первые 54 байта файла .cmp такие же как и файла .bmp
    1. Далее идёт пиксельная информация: каждый пиксель теперь кодируется следующим образом: сначала 3 байта цвета пикселя в палитре rgb, а потом один байт - количество раз, которое этот цвет встречается подряд

    Результатом разжатия (функции ```decompress()```) должно стать BMP изображение, разжатое из файла ".cmp"         
    Эти функции должны использовать только функции стандартных библиотек вашего языка программирования.
1. Во сколько раз такой метод сжимает изображения в лучшем случае? а в худшем?

# Задание 3 (Вариант 1)
1. Какие недостатки вы нашли в реализованном алгоритме сжатия? Можно ли улучшить этот алгоритм?
1. Напишите функции ```super_compress(input_image, result_image)```  и ```super_decompress(input_image, result_image)```, которая исправит увиденные вами недостатки алгоритмов и опишите формат файлов, которые получатся в результате сжатия.
1. \* Как ещё можно сжать изображение так, чтобы сохранить всю изначальную информацию? Напишите функции```extra_compress(input_image, result_image)```  и ```extra_decompress(input_image, result_image)```, которая реализует ваши идеи и опишите формат файлов, которые получатся в результате сжатия.

# Задание 3 (Вариант 2)
1. Какие недостатки вы нашли в реализованном алгоритме сжатия? Можно ли улучшить этот алгоритм?     

Некоторые проблемы можно устранить если не пытаться при сжатии сохранять всю информацию, имеющуюся в изображении. Например, если нескольким пикселям, которые имели раньше разные цвета при сжатии присваивать одинаковый.

2. Напишите функцию ```super_compress(input_image, result_image)``` , которая улучшит степень сжатия файла алгоритмом из задания 2, засчёт потери части информации, и функцию ```super_decompress(input_image, result_image)```, которая восстановит изображение BMP по сохранившейся информации. Постарайтесь сделать так, чтобы изначальное и изображение в результате последовательного сжатия и разжатия не сильно отличались друг от друга.