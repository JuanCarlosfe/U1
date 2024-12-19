import glfw
from OpenGL.GL import *
from OpenGL.GLU import gluNewQuadric, gluSphere, gluPerspective
import sys

# Variables globales para el ángulo de rotación y posición de la esfera
window = None
rotation_angle = 0.0  # Ángulo de rotación de la esfera
movement_x = 0.0  # Offset para el movimiento en el eje X
movement_y = 0.0  # Offset para el movimiento en el eje Y
movement_speed_x = 0.01  # Velocidad del movimiento en X
movement_speed_y = 0.01  # Velocidad del movimiento en Y
direction_x = 1  # Dirección del movimiento en X (1 derecha, -1 izquierda)
direction_y = 1  # Dirección del movimiento en Y (1 arriba, -1 abajo)

def init():
    glClearColor(0.0, 0.0, 0.0, 1.0)  # Fondo negro
    glEnable(GL_DEPTH_TEST)            # Activar prueba de profundidad
    glEnable(GL_LIGHTING)              # Activar iluminación
    glEnable(GL_LIGHT0)                # Activar la luz 0

    # Configuración de la perspectiva
    glMatrixMode(GL_PROJECTION)
    gluPerspective(40, 1.0, 0.1, 50.0)
    glMatrixMode(GL_MODELVIEW)

    # Configuración de la luz
    light_pos = [1.0, 1.0, 1.0, 0.0]  # Posición de la luz
    light_color = [1.0, 1.0, 1.0, 1.0]  # Color de la luz blanca
    ambient_light = [0.2, 0.2, 0.2, 1.0]  # Luz ambiental

    glLightfv(GL_LIGHT0, GL_POSITION, light_pos)
    glLightfv(GL_LIGHT0, GL_DIFFUSE, light_color)
    glLightfv(GL_LIGHT0, GL_AMBIENT, ambient_light)

    # Configuración de las propiedades de material
    material_diffuse = [0.5, 0.5, 1.0, 1.0]  # Color difuso (azul claro)
    glMaterialfv(GL_FRONT, GL_DIFFUSE, material_diffuse)

def draw_sphere(radius=0.5, slices=32, stacks=32):
    global rotation_angle, movement_x, movement_y
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
    glLoadIdentity()
    glTranslatef(movement_x, movement_y, -5)  # Mover la esfera en ambas direcciones
    glRotatef(rotation_angle, 0, 1, 0)  # Rotar la esfera sobre su eje Y

    quadric = gluNewQuadric()
    gluSphere(quadric, radius, slices, stacks)  # Dibujar la esfera

    glfw.swap_buffers(window)

def update_motion():
    global rotation_angle, movement_x, movement_y, direction_x, direction_y

    # Actualizar el ángulo de rotación
    rotation_angle += 1
    if rotation_angle >= 360:
        rotation_angle = 0  # Reiniciar el ángulo después de una vuelta completa

    # Actualizar el movimiento en X
    movement_x += movement_speed_x * direction_x
    if movement_x > 1.3:       # Límite derecho
        direction_x = -1       # Cambiar dirección hacia la izquierda
    elif movement_x < -1.3:    # Límite izquierdo
        direction_x = 1        # Cambiar dirección hacia la derecha

    # Actualizar el movimiento en Y
    movement_y += movement_speed_y * direction_y
    if movement_y > 1.3:       # Límite superior
        direction_y = -1       # Cambiar dirección hacia abajo
    elif movement_y < -1.3:    # Límite inferior
        direction_y = 1        # Cambiar dirección hacia arriba

def main():
    global window

    # Inicializar GLFW
    if not glfw.init():
        sys.exit()

    # Crear ventana de GLFW
    width, height = 500, 500
    window = glfw.create_window(width, height, "Esfera en Movimiento y Rotación", None, None)
    if not window:
        glfw.terminate()
        sys.exit()

    glfw.make_context_current(window)
    glViewport(0, 0, width, height)
    init()

    # Bucle principal
    while not glfw.window_should_close(window):
        draw_sphere()
        update_motion()  # Actualizar el movimiento y rotación
        glfw.poll_events()

    glfw.terminate()

if __name__ == "__main__":
    main()
