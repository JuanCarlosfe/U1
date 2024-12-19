import cv2 as cv
import numpy as np

# Cargar la imagen en escala de grises
img = cv.imread('Perrito.jpg', 0)

# Obtener el tamaño de la imagen
x, y = img.shape

# Definir el factor de escala
scale_x, scale_y = 2, 2

# Crear una nueva imagen para almacenar el escalado
scaled_img = np.zeros((int(x * scale_y), int(y * scale_x)), dtype=np.uint8)

# Aplicar el escalado
for i in range(x):
    for j in range(y):
        scaled_img[i*2, j*2] = img[i, j]

filtered_img = np.zeros_like(scaled_img)       

for i in range(1, scaled_img.shape[0] - 1):
    for j in range(1, scaled_img.shape[1] - 1):
        suma = 0
        for k in range(-1, 2):
            for l in range(-1, 2):
                suma += scaled_img[i + k, j + l]
        filtered_img[i, j] = int(suma / 9)

# Mostrar la imagen original, la escalada y la filtrada
cv.imshow('Imagen Original', img)
cv.imshow('Imagen Escalada (modo raw)', scaled_img)
cv.imshow('Imagen Filtrada (convolucion 1/9)', filtered_img)
cv.waitKey(0)
cv.destroyAllWindows()
