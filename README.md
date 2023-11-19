# VC_P5

## Introducción 

El objetivo de esta práctica es realizar la detección de matrículas ya sea desde una imagen o desde un vídeo. Para ello, se han elaborado dos enfoques diferentes. 

1. Tratamos de detectar la matrícula haciendo procesado de la imagen y detectando contornos. 
2. Se ha entrenado "yolo" para que detecte matrículas.

## Detección con procesado de imagen

Para este apartado, hemos utilizado diferentes heurísticas, por ejemplo: la matrícula suele estar en la mitad inferior de la imagen, su contorno tiene forma rectangular y tiene unas proporciones determinadas. Por tanto, el procedimiento seguido ha sido el siguiente:

1. Detectamos un vehículo haciendo uso de "yolov8"
2. Pasamos la imagen del vehículo a nuestra función detectar_matricula()

    2.1 Nos quedamos con la mitad inferior de la imagen y la procesamos para mejorar los contrastes.
   
    2.2 Aplicamos umbralizado.
   
    2.3 Buscamos contornos externos y nos quedamos con los que nos interesan en función de sus proporciones y de si contienen texto (para la detección de caracteres hemos utilziado easyocr).
   
    2.4 Finalmente, hacemos un plot de la imagen con un rectángulo verde sobre la matrícula detectada y devolvemos el texto encontrado en la matrícula.

![Resultado de la detección](https://github.com/ivanom2002/VC_P5/blob/main/resultadoManual.jpg)

## Detección usando yolov8

En primer lugar, tuvimos que encontrar un [conjunto de datos](https://github.com/ivanom2002/VC_P5/edit/main/dataset) que nos permitiera realizar el entrenamiento. Una vez obtuvimos dicho conjunto de datos hicimos el entrenamiento ejecutando la siguiente línea:

```
yolo detect train model=yolov8n.pt data=data/miarchivo.yml imgsz=416 batch=4 device=0[,1,2,3] epochs=40
```

![Resultado del entrenamiento](https://github.com/ivanom2002/VC_P5/blob/main/entrenamiento.jpg)

Una vez entrenado, cargamos el modelo de la siguiente manera:

```
trainedModel = YOLO('runs/detect/train/weights/best.pt')
```

Por úlitmo creamos la función dectar_matricula_yolo() que usa el modelo entrenado para hacer la detección de matrículas sobre una imagen y "easyocr" para leer la matrícula. Obteniendo resultados como el siguiente:

![Resultado de la detección](https://github.com/ivanom2002/VC_P5/blob/main/resultadoYolo.jpg)

## Tracking con yolov8

En este apartado utilizamos la función de tracking que ofrece "yolov8" para poder hacer la detección de matrículas sobre un vídeo. Para ello utilizamos el siguiente código:

```
model = YOLO('runs/detect/train/weights/best.pt')
results = model.track(source="Ruta al vídeo", show=True)
```

## Conclusiones

Para las imágenes que hemos utilizado de prueba se observa que la detección manual ajusta mejor el contorno de la matrícula y permite que easyocr lea de forma más precisa dicha matrícula. Por otro lado, la detección realizada con el modelo "yolov8" entrenado facilita el proceso, ya que, no tenemos que estar detectando el vehículo previamente ni tenemos que estar procesando la imagen en busca de contornos.
