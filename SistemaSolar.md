import numpy as np
import cv2

def generar_punto_elipse(a, b, t, cx=300, cy=300):
    x = int(a * np.cos(t) + cx)  # Desplazamiento para centrar
    y = int(b * np.sin(t) + cy)
    return (x, y)

img_width, img_height = 600, 600

imagen = np.zeros((img_height, img_width, 3), dtype=np.uint8)

orbita_planetas = [80, 120, 160, 200, 250]  # Semiejes mayores
colores_planetas = [(0, 0, 255), (0, 255, 0), (255, 0, 0), (0, 255, 255), (255, 255, 0)]
radius_planetas = [8, 10, 12, 14, 16]
num_puntos = 1000

t_vals = np.linspace(0, 2 * np.pi, num_puntos)

for t in t_vals:
    imagen = np.zeros((img_height, img_width, 3), dtype=np.uint8)

    cv2.circle(imagen, (300, 300), 40, (0, 165, 255), -1)  # Capa externa
    cv2.circle(imagen, (300, 300), 30, (0, 255, 255), -1)  # Capa interna

    for i, a in enumerate(orbita_planetas):
        b = a  # Hacer las órbitas circulares

        for t_tray in t_vals:
            pt_tray = generar_punto_elipse(a, b, t_tray)
            cv2.circle(imagen, pt_tray, radius=1, color=(255, 255, 255), thickness=-1)

        planeta = generar_punto_elipse(a, b, t)

        cv2.circle(imagen, planeta, radius=radius_planetas[i], color=colores_planetas[i], thickness=-1)

    cv2.imshow('Sistema Solar', imagen)

    cv2.waitKey(10)

cv2.destroyAllWindows()
