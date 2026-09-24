#include <stdio.h>

#define TOTAL_EVENTOS 33
#define NUM_EQUIPOS 4
#define NUM_TIPOS_EVENTO 4
#define UMBRAL_SOSPECHOSO 3

// Tipos de evento:
// 'C' = Acceso correcto
// 'I' = Contraseña incorrecta
// 'U' = Usuario inexistente
// 'B' = Acceso bloqueado

// 4.3 Función con paso por VALOR
// Recibe el carácter del evento y retorna la columna correspondiente de la matriz
int obtenerIndiceEvento(char tipoEvento) {
    switch (tipoEvento) {
        case 'C': return 0; // Acceso correcto
        case 'I': return 1; // Contraseña incorrecta
        case 'U': return 2; // Usuario inexistente
        case 'B': return 3; // Acceso bloqueado
        default:  return -1; // Código inválido
    }
}

// 4.4 Función con paso por REFERENCIA (Uso de Apuntadores)
// Recibe un apuntador a una celda de la matriz e incrementa su valor directamente
void actualizarContador(int *celda) {
    if (celda != NULL) {
        (*celda)++;
    }
}

int main() {
    // 4.1 Arreglos unidimensionales - Registros de entrada (log secuencial)
    int listaEquipos[TOTAL_EVENTOS] = {
        0, 1, 0, 1, 2, 3, 1, 3, 0, 1,
        2, 3, 1, 3, 0, 1, 2, 3, 0, 1,
        2, 3, 0, 1, 2, 0, 1, 0, 1, 0,
        1, 2, 0
    };

    char listaEventos[TOTAL_EVENTOS] = {
        'C', 'I', 'C', 'I', 'C', 'U', 'I', 'U', 'C', 'I',
        'C', 'U', 'I', 'U', 'C', 'I', 'U', 'U', 'C', 'B',
        'C', 'B', 'C', 'U', 'C', 'C', 'U', 'C', 'C', 'C',
        'C', 'C', 'C'
    };

    // Nombres de los equipos para presentación
    char nombresEquipos[NUM_EQUIPOS][10] = {"PC-01", "PC-02", "PC-03", "PC-04"};

    // 4.2 Arreglo bidimensional (Matriz equipos x tipos de evento)
    int matrizResumen[NUM_EQUIPOS][NUM_TIPOS_EVENTO] = {0};

    // Procesamiento de los eventos
    for (int i = 0; i < TOTAL_EVENTOS; i++) {
        int idEquipo = *(listaEquipos + i); // Uso de aritmética de apuntadores
        char tipoEvento = *(listaEventos + i);

        // Paso por valor para clasificar evento
        int col = obtenerIndiceEvento(tipoEvento);

        if (idEquipo >= 0 && idEquipo < NUM_EQUIPOS && col != -1) {
            // 4.5 Modificar matriz mediante apuntador (paso por referencia)
            actualizarContador(&matrizResumen[idEquipo][col]);
        }
    }

    // IMPRESIÓN DE RESULTADOS
    printf("=========================================================================\n");
    printf("                        LOG DE EVENTOS PROCESADOS                        \n");
    printf("=========================================================================\n");
    
    // 4.5 Recorrido de los arreglos unidimensionales con aritmética de apuntadores
    int *ptrEq = listaEquipos;
    char *ptrEv = listaEventos;
    for (int i = 0; i < TOTAL_EVENTOS; i++) {
        printf("[%02d] Equipo: PC-%02d | Evento: %c\n", i + 1, *ptrEq + 1, *ptrEv);
        ptrEq++;
        ptrEv++;
    }

    printf("\n=========================================================================\n");
    printf("               RESUMEN FINAL (MATRIZ EQUIPOS x EVENTOS)                  \n");
    printf("=========================================================================\n");
    printf("%-8s | %-10s | %-11s | %-10s | %-10s | %-6s\n", 
           "Equipo", "Correcto", "C. Incorrecta", "U. Inexis.", "Bloqueado", "Total");
    printf("-------------------------------------------------------------------------\n");

    for (int i = 0; i < NUM_EQUIPOS; i++) {
        int totalEquipo = 0;
        
        // Uso de aritmética de apuntadores para recorrer la fila actual
        int *ptrFila = *(matrizResumen + i); 

        for (int j = 0; j < NUM_TIPOS_EVENTO; j++) {
            totalEquipo += *(ptrFila + j);
        }

        printf("%-8s | %-10d | %-13d | %-10d | %-10d | %-6d\n",
               nombresEquipos[i],
               *(ptrFila + 0),
               *(ptrFila + 1),
               *(ptrFila + 2),
               *(ptrFila + 3),
               totalEquipo);
    }

    // 4.6 Regla de detección de comportamientos sospechosos
    printf("\n=========================================================================\n");
    printf("                   DETECCION DE EQUIPOS SOSPECHOSOS                      \n");
    printf("=========================================================================\n");

    for (int i = 0; i < NUM_EQUIPOS; i++) {
        int *ptrFila = *(matrizResumen + i);
        int fallosAcceso = *(ptrFila + 1) + *(ptrFila + 2); // C. Incorrecta + U. Inexistente

        printf("Equipo: %s -> Intentos fallidos: %d | Estado: ", nombresEquipos[i], fallosAcceso);
        
        if (fallosAcceso > UMBRAL_SOSPECHOSO) {
            printf("[SOSPECHOSO] (Supera el umbral de X = %d)\n", UMBRAL_SOSPECHOSO);
        } else {
            printf("[NORMAL]\n");
        }
    }
    printf("=========================================================================\n");

    return 0;
}
