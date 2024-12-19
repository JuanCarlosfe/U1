import cv2 as cv
import numpy as np
import math

img = cv.imread('Kirby.png', 0)

x, y = img.shape

rotated_img = np.zeros((x*4, y*4), dtype=np.uint8)  # Multiplicar por 4 para dar más espacio por el escalado
xx, yy = rotated_img.shape

cx, cy = int(x // 2), int(y // 2)

angle1 = 30   # Rotación +30 grados
angle2 = -60  # Rotación -60 grados
theta1 = math.radians(angle1)
theta2 = math.radians(angle2)

scale = 2  # Escalado a 2

for i in range(x):
    for j in range(y):
        new_x = int(((j - cx) * math.cos(theta1) - (i - cy) * math.sin(theta1)) * scale + cx)
        new_y = int(((j - cx) * math.sin(theta1) + (i - cy) * math.cos(theta1)) * scale + cy)
        
        if 0 <= new_x < yy and 0 <= new_y < xx:
            rotated_img[new_y, new_x] = img[i, j]

for i in range(x):
    for j in range(y):
        new_x = int(((j - cx) * math.cos(theta2) - (i - cy) * math.sin(theta2)) * scale + cx)
        new_y = int(((j - cx) * math.sin(theta2) + (i - cy) * math.cos(theta2)) * scale + cy)
        
        if 0 <= new_x < yy and 0 <= new_y < xx:
            rotated_img[new_y, new_x] = img[i, j]

cv.imshow('Imagen Original', img)
cv.imshow('Imagen 2', rotated_img)
cv.waitKey(0)
cv.destroyAllWindows()
