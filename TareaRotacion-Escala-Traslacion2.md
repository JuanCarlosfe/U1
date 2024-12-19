import cv2 as cv
import numpy as np
import math

img = cv.imread('Kirby.png', 0)

x, y = img.shape

rotated_img = np.zeros((x*2, y*2), dtype=np.uint8)
xx, yy = rotated_img.shape

cx, cy = int(x // 2), int(y // 2)

angle = 70  # Cambiar a 60 grados
theta = math.radians(angle)

tx = 20  # Traslación en x
ty = 20  # Traslación en y

scale = 2  # Escalado a 1/5

for i in range(x):
    for j in range(y):
        new_x = int(((j - cx) * math.cos(theta) - (i - cy) * math.sin(theta)) * scale + cx + tx)
        new_y = int(((j - cx) * math.sin(theta) + (i - cy) * math.cos(theta)) * scale + cy + ty)
        
        if 0 <= new_x < yy and 0 <= new_y < xx:
            rotated_img[new_y, new_x] = img[i, j]

cv.imshow('Imagen Original', img)
cv.imshow('Imagen Rotada, Trasladada y Escalada', rotated_img)
cv.waitKey(0)
cv.destroyAllWindows()
