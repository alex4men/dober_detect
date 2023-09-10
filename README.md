# Dober Team ML-service для кейса Netris хакатона Цифровой прорыв УрФО 2023

[//]: # (Image References)
[image1]: ./misc/test_out.jpg "Test output"


![test output][image1]


## Описание

В качестве детектора была выбрана готовая модель для Zero-Shot Detection [Grounding DINO](https://github.com/IDEA-Research/GroundingDINO), т.к. размеченного обучающего датасета не было, как и ресурсов чтобы быстро его разметить.

Для ускорения обработки мы обрабатываем 1 кадр за секунду. Скорость обработки кадра на сервере как раз около 1 кадра в секунду.

Между детекцией и трекингом добавлен этап Non-maxima suppression, т.к. детектор часто выдает несколько боксов на одной позиции.

В качестве трекера используется Deep-SORT.

Уникальной особенностью данного сервиса является инвариантность к классам детекции, так и к ракурсам съемки. При помощи текстового описания требуемого класса (на английском языке) можно детектировать объекты, под которые нейросеть не обучалась.

В дальнейшем, для классификации состояния строительной техники.

Протестирована совместимость с python3.8 и Ubuntu 20.04.

## Установка

```bash
git clone https://github.com/alex4men/dober_detect.git
cd dober_detect
python3 -m venv --system-site-packages venv
source venv/bin/activate
pip install -U pip
pip install -r requirements.txt
```

## Тестирование

В файле pipeline.py можно задать путь к входному и выходному видео, а так же список классов для поиска в самом конце файла.

```bash
cd src
python pipeline.py
```

## Деплой в продакшн

На голом железе

```bash
uvicorn service:app --host 0.0.0.0 --port 3000
```

Либо в Докере

```bash
docker build -t dober_ml_img .
docker run -d --rm --name dober_ml_cont -p 8086:8086 dober_ml_img
```


