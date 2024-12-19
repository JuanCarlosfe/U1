import cv2 as cv
import numpy as np

img = np.ones((500, 500, 3), dtype=np.uint8) * 255

cv.ellipse(img, (250, 250), (150, 100), 0, 0, 360, (0, 0, 0), thickness=5)

cv.ellipse(img, (250, 250), (140, 90), 0, 0, 360, (255, 255, 255), thickness=-1)

cv.circle(img, (250, 250), 60, (0, 234, 21), -1)

for angle in range(0, 360, 20):
    x_end = int(250 + 60 * np.cos(np.radians(angle)))
    y_end = int(250 + 60 * np.sin(np.radians(angle)))
    cv.line(img, (250, 250), (x_end, y_end), (0, 200, 0), 1)

cv.circle(img, (250, 250), 20, (0, 0, 0), -1)

cv.circle(img, (240, 240), 8, (255, 255, 255), -1)

cv.imshow('Ojo Verde', img)
cv.waitKey()
cv.destroyAllWindows()
