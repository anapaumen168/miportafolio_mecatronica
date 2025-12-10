# Código en Python

Este código se encarga de la detección visual de la pelota y determina cuanto y como deben moverse los motores. Procesa la información visual de la pelota y calcula las órdenes de movimiento exactas que la plataforma necesita para mantener el equilibrio.

```codigo

import cv2                 # Librería para la cámara, ventanas y procesamiento de imagen
import numpy as np        # Usada aquí para crear rangos HSV
import serial             # Para comunicación serial con Arduino
import time               # Para medir tiempos y hacer delays precisos

# ----------------------------------------------------------------------------------
# ---------------------------- CONFIGURACIÓN GENERAL --------------------------------
# ----------------------------------------------------------------------------------

PUERTO = 'COM4'    # Puerto serial donde está conectado el Arduino
BAUD   = 115200    # Velocidad de comunicación

VELOCIDAD_BAJADA = 150  # Velocidad fija para bajar plataforma en la fase de calibración

# Intento de conectarse al Arduino
try:
    # Se abre el puerto con el baudrate y pequeños timeouts
    ser = serial.Serial(PUERTO, BAUD, timeout=0.05, write_timeout=0)
    print(f" CONECTADO A {PUERTO} (MODO PID)")
except Exception as e:
    # Si falla, se imprime el error y se sale del programa
    print(f" ERROR: {e}")
    exit()

# Se inicializa la cámara. El segundo parámetro obliga a usar DirectShow en Windows
cap = cv2.VideoCapture(1, cv2.CAP_DSHOW)

# ----------------------------------------------------------------------------------
# ----------------------------- RANGO DE COLOR ROJO ---------------------------------
# ----------------------------------------------------------------------------------
# Estos valores corresponden al color rojo en espacio HSV.
# El rojo está dividido en dos rangos porque HSV da un salto en H (0 a 180).
bajo1 = np.array([0, 120, 70])
alto1 = np.array([10, 255, 255])

bajo2 = np.array([170, 120, 70])
alto2 = np.array([180, 255, 255])

# ----------------------------------------------------------------------------------
# -------------------------- CREACIÓN DE INTERFAZ GRÁFICA ---------------------------
# ----------------------------------------------------------------------------------

def nothing(x): 
    """Función vacía necesaria para los sliders de OpenCV."""
    pass

cv2.namedWindow("CONTROL PID")         # Se crea la ventana interactiva
cv2.resizeWindow("CONTROL PID", 600, 600)

# ----------------------------- SLIDERS DE CONTROL PID -----------------------------
# Cada slider tiene un valor entero, luego se convrtiran a float

cv2.createTrackbar("Kp", "CONTROL PID", 150, 500, nothing)   # 150 -> 1.5
cv2.createTrackbar("Kd", "CONTROL PID", 300, 1000, nothing)  # 300 -> 3.0
cv2.createTrackbar("Ki", "CONTROL PID", 0, 100, nothing)     # 0 -> 0.000

# ----------------------------------------------------------------------------------
# ------------------------- INVERSIONES DE SENTIDO DE GIRO --------------------------
# ----------------------------------------------------------------------------------
# Permiten invertir un motor si gira al revés.
pol_m1 = 1; pol_m2 = 1; pol_m3 = 1; pol_m4 = 1

def toggle_m1(v):  # Cambia entre 1 y -1
    global pol_m1
    pol_m1 = -1 if v==1 else 1

def toggle_m2(v):
    global pol_m2
    pol_m2 = -1 if v==1 else 1

def toggle_m3(v):
    global pol_m3
    pol_m3 = -1 if v==1 else 1

def toggle_m4(v):
    global pol_m4
    pol_m4 = -1 if v==1 else 1

# Sliders:
cv2.createTrackbar("Inv M1 (InfIzq)", "CONTROL PID", 0, 1, toggle_m1)
cv2.createTrackbar("Inv M2 (InfDer)", "CONTROL PID", 0, 1, toggle_m2)
cv2.createTrackbar("Inv M3 (SupDer)", "CONTROL PID", 0, 1, toggle_m3)
cv2.createTrackbar("Inv M4 (SupIzq)", "CONTROL PID", 0, 1, toggle_m4)

# ==================================================================================
#                           FASE 1: CALIBRACIÓN INICIAL
# ==================================================================================

print("BAJANDO PLATAFORMA...")
start_cal = time.time()   # Guardamos tiempo de inicio para contar 15s

# Este ciclo baja la plataforma durante exactamente 15 segundos
while (time.time() - start_cal) < 15:
    ret, frame = cap.read()       # Leemos frame de cámara
    if not ret: break

    frame = cv2.resize(frame, (640, 480)) # Se ajusta el tamaño de la imagen

    # Enviar misma velocidad a los 4 motores para bajar parejo
    if ser.is_open:
        msg = f"{VELOCIDAD_BAJADA},{VELOCIDAD_BAJADA},{VELOCIDAD_BAJADA},{VELOCIDAD_BAJADA}\n"
        ser.write(msg.encode())

    # Muestra cuenta regresiva
    time_left = 15 - int(time.time() - start_cal)
    cv2.putText(frame, f"CALIBRANDO: {time_left}s", (50, 240),
                cv2.FONT_HERSHEY_SIMPLEX, 1.5, (0, 0, 255), 3)

    cv2.imshow("CONTROL PID", frame)

    if cv2.waitKey(10) & 0xFF == ord('q'): exit()

# Frenar al terminar la calibración
if ser.is_open:
    ser.write(b"0,0,0,0\n")
    time.sleep(0.2)

print("INICIANDO CONTROL PID.")

# ==================================================================================
#                              FASE 2: BUCLE PID
# ==================================================================================

# Variables de estado del PID
last_send = 0     # Último tiempo en que enviamos datos a Arduino
last_err_x = 0    # Error previo en X
last_err_y = 0    # Error previo en Y
integ_x = 0       # Acumulador integral X
integ_y = 0       # Acumulador integral Y
last_time_pid = time.time()  # Tiempo previo de ciclo PID

while True:
    ret, frame = cap.read()
    if not ret: break

    # Se reduce frame para acelerar procesamiento
    proc_frame = cv2.resize(frame, (320, 240))
    h, w, _ = proc_frame.shape
    cx, cy = w // 2, h // 2  # Centro de imagen, objetivo del PID

    # ----------------------------- LECTURA DE SLIDERS -----------------------------
    Kp = cv2.getTrackbarPos("Kp", "CONTROL PID") / 100.0
    Kd = cv2.getTrackbarPos("Kd", "CONTROL PID") / 100.0
    Ki = cv2.getTrackbarPos("Ki", "CONTROL PID") / 1000.0

    # --------------------------- DETECCIÓN DEL COLOR ROJO --------------------------
    hsv = cv2.cvtColor(proc_frame, cv2.COLOR_BGR2HSV)
    mask = cv2.add(cv2.inRange(hsv, bajo1, alto1), cv2.inRange(hsv, bajo2, alto2))
    mask = cv2.erode(mask, None, iterations=1)    # Limpieza de ruido
    mask = cv2.dilate(mask, None, iterations=2)

    # Encontrar contornos de la pelota
    cnts, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    # Inicializació de motores
    m1=m2=m3=m4=0
    ball_found = False

    # ----------- Cálculo del tiempo delta para el PID -----------
    current_time = time.time()
    dt = current_time - last_time_pid
    if dt == 0: dt = 0.001  # Evita división entre cero
    last_time_pid = current_time

    # ==================================================================================
    #                             SI SE ENCONTRÓ LA PELOTA
    # ==================================================================================
    if len(cnts) > 0:
        # Elegir el contorno más grande: asumimos que es la pelota
        c = max(cnts, key=cv2.contourArea)
        ((x, y), r) = cv2.minEnclosingCircle(c)  # Centro y radio

        if r > 4:
            ball_found = True

            # Dibujar círculo en imagen grande
            cv2.circle(frame, (int(x)*2, int(y)*2), int(r)*2, (0, 255, 0), 2)
            cv2.line(frame, (cx*2, cy*2), (int(x)*2, int(y)*2), (255, 0, 0), 2)

            # --------------------------- CÁLCULO DEL ERROR --------------------------
            err_x = int(x - cx)
            err_y = int(y - cy)

            # ----------------------------- PID EN EJE X -----------------------------
            d_x = (err_x - last_err_x) / dt
            integ_x = max(min(integ_x + (err_x * dt), 200), -200)  
            output_x = (err_x * Kp) + (d_x * Kd) + (integ_x * Ki)
            last_err_x = err_x

            # ----------------------------- PID EN EJE Y -----------------------------
            d_y = (err_y - last_err_y) / dt
            integ_y = max(min(integ_y + (err_y * dt), 200), -200)
            output_y = (err_y * Kp) + (d_y * Kd) + (integ_y * Ki)
            last_err_y = err_y

            # ------------------------ MEZCLA PARA LOS 4 MOTORES ----------------------
            # X+: baja izquierda, sube derecha
            # Y+: baja arriba, sube abajo

            m1 = int( -output_y + output_x )  # Inf Izq
            m2 = int( -output_y - output_x )  # Inf Der
            m3 = int(  output_y - output_x )  # Sup Der
            m4 = int(  output_y + output_x )  # Sup Izq

            # Limitar valores entre -255 y 255
            m1 = max(min(m1, 255), -255)
            m2 = max(min(m2, 255), -255)
            m3 = max(min(m3, 255), -255)
            m4 = max(min(m4, 255), -255)

    # ==================================================================================
    #                       ENVÍO DE DATOS A ARDUINO (60 Hz)
    # ==================================================================================
    if ser.is_open and (time.time() - last_send > 0.015):
        try:
            if not ball_found:
                # Si no detectamos pelota, detenemos el programa
                msg = "0,0,0,0\n"
            else:
                # Enviamos valores ajustados por inversión por motor
                msg = f"{m1*pol_m1},{m2*pol_m2},{m3*pol_m3},{m4*pol_m4}\n"

            ser.write(msg.encode())
            last_send = time.time()

            # Mostrar info PID en pantalla
            info = f"P:{Kp:.2f} D:{Kd:.2f} | OutX:{int(output_x)}"
            cv2.putText(frame, info, (10, 450), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 255), 2)

        except:
            pass  # Ignoramos errores de envío

    cv2.imshow("CONTROL PID", frame)

    # Salida del programa
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# Al finalizar, apagar motores y cerrar puerto
if ser.is_open:
    ser.write(b"0,0,0,0\n")
    ser.close()

cv2.destroyAllWindows()

```