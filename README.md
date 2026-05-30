# Pacman_python
El clásico juego de Pacman traducido al lenguaje de python

import pygame
import sys

# ========================================================
# CONFIGURACION GENERAL DEL JUEGO DE PACMAN
# ========================================================
TILE = 32
FILAS = 15
COLUMNAS = 19

ANCHO = COLUMNAS * TILE
ALTO = (FILAS * TILE) + 40
FPS = 10

# Colores
NEGRO = (0, 0, 0)
AZUL = (0, 0, 200)
BLANCO = (255, 255, 255)
AMARILLO = (255, 255, 0)
GRIS_OSCURO = (30, 30, 30)

# ====================================================
#   MAPA DEL JUEGO
# ====================================================
# Convertimos a lista de listas para poder modificar los puntos
mapa = [
    list("###################"),
    list("#........#........#"),
    list("#.....#..#..#####.#"),
    list("#.................#"),
    list("#.###.#######.###.#"),
    list("#.....#.....#.....#"),
    list("#####.#.###.#.#####"),
    list("#.................#"),
    list("#.#####.###.#####.#"),
    list("#.....#.....#.....#"),
    list("#.###.#######.###.#"),
    list("#.................#"),
    list("#.#####..#..#####.#"),
    list("#........#........#"),
    list("###################")
]

# ===========================================
# CLASE PACMAN (Equivalente al struct)
# ===========================================
class Pacman:
    def __init__(self):
        self.fila = 1
        self.columna = 1
        self.dirX = 0
        self.dirY = 0

# ==============================================
# FUNCIONES DE LÓGICA
# ==============================================
def es_muro(fila, columna):
    if fila < 0 or fila >= FILAS or columna < 0 or columna >= COLUMNAS:
        return True
    return mapa[fila][columna] == '#'

def mover_pacman(p):
    nueva_fila = p.fila + p.dirY
    nueva_columna = p.columna + p.dirX
    
    if not es_muro(nueva_fila, nueva_columna):
        p.fila = nueva_fila
        p.columna = nueva_columna

def comer_punto(p):
    global score
    if mapa[p.fila][p.columna] == '.':
        mapa[p.fila][p.columna] = ' '
        return 10
    return 0

# ===============================================
# FUNCIONES DE DIBUJO
# ===============================================
def dibujar_mapa(pantalla):
    for f in range(FILAS):
        for c in range(COLUMNAS):
            x = c * TILE
            y = f * TILE
            if mapa[f][c] == '#':
                pygame.draw.rect(pantalla, AZUL, (x, y, TILE, TILE))
            elif mapa[f][c] == '.':
                pygame.draw.circle(pantalla, BLANCO, (x + TILE // 2, y + TILE // 2), 4)

def dibujar_pacman(pantalla, p):
    centro_x = p.columna * TILE + TILE // 2
    centro_y = p.fila * TILE + TILE // 2
    # Cuerpo
    pygame.draw.circle(pantalla, AMARILLO, (centro_x, centro_y), TILE // 2 - 4)
    # Ojo
    pygame.draw.circle(pantalla, NEGRO, (centro_x + 5, centro_y - 5), 2)

# ====================================================
# FUNCIÓN PRINCIPAL
# ====================================================
def main():
    pygame.init()
    pantalla = pygame.display.set_mode((ANCHO, ALTO))
    pygame.display.set_caption("Pac-Man Python")
    reloj = pygame.time.Clock()
    
    # Cargar recursos
    try:
        fuente = pygame.font.SysFont("arial", 20)
        mi_imagen = pygame.image.load("Logo.png")
        mi_imagen = pygame.transform.scale(mi_imagen, (50, 30))
    except:
        fuente = pygame.font.Font(None, 24)
        mi_imagen = None
        print("Aviso: Logo.png no encontrado o error en fuente.")

    pacman = Pacman()
    score = 0
    salir = False

    while not salir:
        # 1. EVENTOS
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                salir = True
            elif evento.type == pygame.KEYDOWN:
                if evento.key == pygame.K_UP:
                    pacman.dirX, pacman.dirY = 0, -1
                elif evento.key == pygame.K_DOWN:
                    pacman.dirX, pacman.dirY = 0, 1
                elif evento.key == pygame.K_LEFT:
                    pacman.dirX, pacman.dirY = -1, 0
                elif evento.key == pygame.K_RIGHT:
                    pacman.dirX, pacman.dirY = 1, 0
                elif evento.key == pygame.K_ESCAPE:
                    salir = True

        # 2. ACTUALIZACIÓN (Lógica)
        mover_pacman(pacman)
        score += comer_punto(pacman)

        # 3. DIBUJO
        pantalla.fill(NEGRO)
        
        dibujar_mapa(pantalla)
        dibujar_pacman(pantalla, pacman)
        
        # Barra inferior Score
        pygame.draw.rect(pantalla, GRIS_OSCURO, (0, FILAS * TILE, ANCHO, 40))
        
        # Texto Nombre
        txt_nombre = fuente.render("Fernando Prado", True, AMARILLO)
        pantalla.blit(txt_nombre, (ANCHO - txt_nombre.get_width() - 10, 10))
        
        # Score
        txt_score = fuente.render(f"Score: {score}", True, BLANCO)
        pantalla.blit(txt_score, (10, FILAS * TILE + 10))
        
        # Imagen Logo
        if mi_imagen:
            pantalla.blit(mi_imagen, (ANCHO - 60, ALTO - 35))

        pygame.display.flip()
        reloj.tick(FPS) # Controla la velocidad del juego

    pygame.quit()
    sys.exit()

if __name__ == "__main__":
    main()
