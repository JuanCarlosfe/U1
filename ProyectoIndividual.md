import numpy as np
import cv2 as cv
import math

cap = cv.VideoCapture(0)

lk_params = dict(winSize=(15, 15), maxLevel=2,
                 criteria=(cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 0.03))

ret, first_frame = cap.read()
if not ret:
    print("Error: No se pudo acceder a la cámara")
    cap.release()
    exit()

prev_gray = cv.cvtColor(first_frame, cv.COLOR_BGR2GRAY)

height, width = first_frame.shape[:2]
hand_points = np.float32([[[width // 2, height // 2]]])

# Cuadrado
square_center = np.array([width // 2, height // 2], dtype=np.float32)
square_size = 100  
default_angle = 0
rotation_angle = default_angle
scaling_factor = 1.0
translation_x = 0
translation_y = 0

while True:
    ret, frame = cap.read()
    if not ret:
        break

    frame = cv.flip(frame, 1)

    gray_frame = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)

    new_hand_points, st, err = cv.calcOpticalFlowPyrLK(prev_gray, gray_frame, hand_points, None, **lk_params)

    # Dibujar el cuadrado en camara
    half_size = square_size // 2
    top_left = (int(square_center[0] - half_size), int(square_center[1] - half_size))
    bottom_right = (int(square_center[0] + half_size), int(square_center[1] + half_size))
    cv.rectangle(frame, top_left, bottom_right, (0, 255, 0), 2)

    # Verificar si la esfera está dentro del cuadrado
    if new_hand_points is not None and st[0] == 1:
        x, y = new_hand_points[0][0]

        frame = cv.circle(frame, (int(x), int(y)), 10, (0, 0, 255), -1)

        # Verificar si la esfera está fuera de la ventana de la cámara
        if x < 0 or x > width or y < 0 or y > height:
            hand_points = np.float32([[[width // 2, height // 2]]])
            x, y = width // 2, height // 2

        # Comprobar si la esfera está dentro del cuadrado
        if top_left[0] <= x <= bottom_right[0] and top_left[1] <= y <= bottom_right[1]:
            # Rotación
            dx, dy = new_hand_points[0][0] - hand_points[0][0]
            rotation_angle += dx * 0.5  
            rotation_angle %= 360  
            scaling_factor = 1.0
            translation_x = 0
            translation_y = 0
        else:
            # Traslación y Escalamiento
            dx, dy = new_hand_points[0][0] - hand_points[0][0]

            if abs(dx) > abs(dy):  # Movimiento horizontal 
                translation_x += dx * 0.3
            else:  # Movimiento vertical
                scaling_factor += dy * 0.01
                scaling_factor = max(0.5, min(scaling_factor, 2.0))

        hand_points = new_hand_points

    canvas = np.zeros((height, width, 3), dtype=np.uint8)
    points = np.array([
        [-half_size, -half_size],
        [half_size, -half_size],
        [half_size, half_size],
        [-half_size, half_size]
    ])

    # Aplicar la rotación
    rotation_matrix = np.array([
        [math.cos(math.radians(rotation_angle)), -math.sin(math.radians(rotation_angle))],
        [math.sin(math.radians(rotation_angle)), math.cos(math.radians(rotation_angle))]
    ])
    rotated_points = np.dot(points, rotation_matrix.T)

    # Aplicar el escalamiento
    scaled_points = rotated_points * scaling_factor

    # Aplicar la traslación
    transformed_points = scaled_points + [translation_x, translation_y]

    final_points = (transformed_points + square_center).astype(int)

    # Dibujar el cuadrado
    canvas = cv.polylines(canvas, [final_points], isClosed=True, color=(0, 255, 0), thickness=3)

    cv.imshow("Camara", frame)
    cv.imshow("Dibujo", canvas)

    prev_gray = gray_frame.copy()

    if cv.waitKey(30) & 0xFF == 27:
        break

cap.release()
cv.destroyAllWindows()
