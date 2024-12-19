import numpy as np
import cv2 as cv

cap = cv.VideoCapture(0)

lk_params = dict(winSize=(15, 15), maxLevel=2,
                 criteria=(cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 0.03))

ret, first_frame = cap.read()
prev_gray = cv.cvtColor(first_frame, cv.COLOR_BGR2GRAY)

ball_pos = np.array([[250, 250]], dtype=np.float32)
ball_pos = ball_pos[:, np.newaxis, :]

while True:
    ret, frame = cap.read()
    if not ret:
        break

    height, width = frame.shape[:2]
    frame = cv.flip(frame, 1)

    gray_frame = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)

    new_ball_pos, st, err = cv.calcOpticalFlowPyrLK(prev_gray, gray_frame, ball_pos, None, **lk_params)

    if new_ball_pos is not None:
        ball_pos = new_ball_pos

        a, b = ball_pos.ravel()
        frame = cv.circle(frame, (int(a), int(b)), 30, (0, 255, 0), -1)

        if a < width // 2 and b < height // 2:
            cv.putText(frame, '1', (50, 50), cv.FONT_HERSHEY_SIMPLEX, 2, (0, 255, 255), 3, cv.LINE_AA)
        elif a >= width // 2 and b < height // 2:
            cv.putText(frame, '2', (width // 2 + 50, 50), cv.FONT_HERSHEY_SIMPLEX, 2, (0, 255, 255), 3, cv.LINE_AA)
        elif a < width // 2 and b >= height // 2:
            cv.putText(frame, '3', (50, height // 2 + 50), cv.FONT_HERSHEY_SIMPLEX, 2, (0, 255, 255), 3, cv.LINE_AA)
        else:
            cv.putText(frame, '4', (width // 2 + 50, height // 2 + 50), cv.FONT_HERSHEY_SIMPLEX, 2, (0, 255, 255), 3, cv.LINE_AA)

    cv.line(frame, (width // 2, 0), (width // 2, height), (255, 0, 0), 2)
    cv.line(frame, (0, height // 2), (width, height // 2), (255, 0, 0), 2)

    cv.imshow('Pelota en movimiento', frame)

    prev_gray = gray_frame.copy()

    if cv.waitKey(30) & 0xFF == 27:
        break

cap.release()
cv.destroyAllWindows()
