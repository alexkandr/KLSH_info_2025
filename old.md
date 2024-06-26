# Задание 1 (Варинат 1):   
Напишите функцию (метод) ```half_image(input_image, result_image)```
, которая заливает верхнюю половину BMP изображения чёрным цветом.    
У этой функции должно быть два аргумета:     
- ```input_image : string``` - относительный или абсолютный путь к BMP изображению описанного выше формата. Известно, что изображение имеет чётное колчиество пикселей по высоте.
- ```result_image : string``` - относительный или абсолютный путь, по которому будет записан результат выполнения функции.   
    
Эта функция (метод) должен использовать только функции стандартных библиотек вашего языка программирования.

# Возможное решение на python
```Python
def half_image(input_image : str, result_image: str):
    with open(input_image, 'rb') as inp:
        header = inp.read(54)
        width = int.from_bytes(header[18:22], byteorder='little')
        heigth = int.from_bytes(header[22:26], byteorder='little', signed=True)
        alignment = width * 3 % 4
        with open(result_image, 'wb') as result:
            result.writelines([header])
            
            for i in range(heigth):
                if i >= heigth/2: 
                    result.writelines([b'\x00' * width*3])
                    inp.seek(width*3,1)
                else:
                    result.writelines([inp.read(width*3)])
                result.writelines([inp.read(alignment)])
                
half_image('logo.bmp', 'halfed_logo.bmp')
```
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


# Задание 3 (Вариант 2)
1. Какие недостатки вы нашли в реализованном алгоритме сжатия? Можно ли улучшить этот алгоритм?     

1. Напишите функции ```super_compress(input_image, result_image)```  и ```super_decompress(input_image, result_image)```, которая исправит увиденные вами недостатки алгоритмов и опишите формат файлов, которые получатся в результате сжатия.
1. Докажите, что исправленный алгоритм лучше и оцените, во сколько раз он лучше сжимает файлы