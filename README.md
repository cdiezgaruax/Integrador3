# **Integrador3**
[Repositorio en GitHub](https://github.com/cdiezgaruax/Integrador3)

---

## **Trabajos prácticos**

### 1. **Carga de scripts en tiny-lisp**
El módulo `Labmain.cpp` define la función `load_script()`, utilizada para cargar un script en la memoria y aplicarle coloración sintáctica. Esta función se basa en la librería estándar de C.

Debes implementar las funciones `load_script()` y `load_script(filename, true)` en CLion para abrir y leer archivos de texto. Las funcionalidades esperadas son:

- **Primer método:** Solicita el nombre de un archivo al usuario.
- **Segundo método:** Muestra el contenido del archivo si `show_script` es verdadero. Ambos métodos deben cargar el script en la consola.

```cpp
void load_script(const char* filename, bool show_script = false);
void load_script();
```

---

### 2. **Asegurando la robustez del código**
Asegúrate de que tu código maneje correctamente diferentes tipos de errores de entrada, tales como:

- Nombre de archivo inexistente.
- Archivo que no se puede abrir.
- Error durante la lectura del archivo.

**Recomendaciones:**
- Agrega comprobaciones adicionales para gestionar los fallos de manera más sofisticada.
- Utiliza funciones estándar de C/C++ como `fopen`, `printf` y `scanf`.

---

## **Rúbrica**
### **Carga de scripts (50 puntos)**
1. Implementación correcta de `load_script(const char* filename, bool show_script = false)`:
   - Carga correctamente el archivo y muestra su contenido si `show_script` es verdadero. **(25 puntos)**
2. Implementación correcta de `load_script()`:
   - Solicita al usuario un nombre de archivo, llama a `load_script` y maneja errores apropiadamente. **(25 puntos)**

### **Manejo de errores (50 puntos)**
1. Manejo de errores para archivo inexistente. **(15 puntos)**
2. Manejo de errores para archivo no accesible. **(15 puntos)**
3. Manejo de errores durante la lectura del archivo. **(20 puntos)**

**Total: 100 puntos**

---

## **Propuesta de solución**

En CLion, utiliza las funciones estándar para evitar dependencias específicas de Microsoft. Ejemplo de implementación:

```cpp
#include <iostream>
#include <string>
#include <cstdio>

using namespace std;

struct ColorConsole
{
    static constexpr auto fg_blue = "\033[34m";
    static constexpr auto bg_white = "\033[47m";
};

struct ConsoleBox
{
    void new_text() { /*...*/ }
    void set_text(const string &text) { cout << text << endl; }
};

ConsoleBox *consoleBox = new ConsoleBox; // Suposición: ya inicializado.

void load_script(const char* filename, bool show_script = false)
{
    string script;
    FILE* f = nullptr;
    try
    {
        f = fopen(filename, "rb");
        if (!f)
        {
            cerr << "Error al abrir el archivo: " << filename << endl;
            return;
        }

        int c;
        char buf[4001];
        while ((c = fread(buf, 1, 4000, f)) > 0)
        {
            buf[c] = 0;
            script.append(buf);
        }
        fclose(f);
        f = nullptr;

        if (show_script)
        {
            cout << ColorConsole::fg_blue << ColorConsole::bg_white;
            cout << script << endl;
        }
        consoleBox->new_text();
        consoleBox->set_text(script);
    }
    catch (...)
    {
        cerr << "Error durante la lectura del archivo." << endl;
        if (f) fclose(f);
    }
}

void load_script()
{
    char filename[500];
    printf("Archivo: ");
    scanf("%499s", filename);
    load_script(filename, true);
}
```

### **Consideraciones finales:**
- Este código debería compilar y ejecutarse en CLion sin problemas.
- No requiere la directiva `_CRT_SECURE_NO_WARNINGS`.
- Podrías mejorar el manejo de errores y adaptar el código a necesidades específicas.
  
---
# **Integrador3: Evaluación y Corrección**

### **Repositorio del ejercicio evaluado**
[Repositorio de Jun](https://github.com/junx21/Integrador_T3/tree/main)

---

### **Análisis y evaluación del código**

#### **Carga de scripts (50 puntos)**:
1. **Implementación de `load_script(const char* filename, bool show_script = false)` (25/25)**:
   - La función cumple con los requisitos de lectura y procesamiento del archivo.
   - Maneja el flujo básico de apertura, lectura y cierre del archivo.
   - Implementación clara y funcional.

2. **Implementación de `load_script()` (20/25)**:
   - La función solicita el nombre del archivo al usuario e invoca la otra versión de `load_script`.
   - Sin embargo, **la interacción directa con el usuario** (`scanf`) debería delegarse al `main` para mantener una estructura modular. Esto afecta la reutilización y el diseño.

**Subtotal: 45/50.**

---

#### **Manejo de errores (50 puntos)**:
1. **Archivo inexistente (15/15)**:
   - Implementación correcta con mensajes de error descriptivos para archivos no encontrados.
   
2. **Error al abrir el archivo (15/15)**:
   - Maneja adecuadamente casos en los que el archivo no puede abrirse.
   
3. **Error durante la lectura del archivo (20/20)**:
   - Utiliza un bloque `try-catch` y asegura la liberación de recursos (`fclose`) para evitar fugas.

**Subtotal: 50/50.**

---

#### **Nota final: 95/100.**

---

### **Justificación de la Nota**

1. **Funcionalidad sólida y robustez**:
   - La implementación cumple los objetivos del ejercicio y maneja escenarios de error comunes con mensajes claros.
   - Utiliza estructuras como `try-catch` para garantizar que los recursos se liberen incluso si ocurre un error.

2. **Separación modular mejorada**:
   - Dividir el código en dos archivos (`main.cpp` y `ejercicio.cpp`) es una práctica excelente que mejora la claridad y mantenimiento del código.

3. **Problema con la interacción directa en `load_script()`**:
   - Aunque funciona, la entrada del usuario (`scanf`) debería gestionarse exclusivamente en `main`. Esto reduce las dependencias de las funciones principales y permite un diseño más flexible y reutilizable.

4. **Estilo y legibilidad**:
   - Código claro, con nombres descriptivos y un estilo consistente.
   - La adición de colores en la salida mejora la experiencia del usuario y es un detalle funcional atractivo.

---

### **Recomendaciones de Mejora**

1. **Modularización de la interacción con el usuario**:
   - Mover la solicitud de entrada del usuario a `main.cpp` para seguir las pautas de diseño modular:
     ```cpp
     int main()
     {
         char filename[500];
         std::cout << "Ingrese el nombre del archivo: ";
         std::cin.getline(filename, 500);

         load_script(filename, true);
         return 0;
     }
     ```
   - Esto deja a las funciones en `ejercicio.cpp` exclusivamente para la lógica de carga y manejo de scripts.

2. **Uso de herramientas modernas de C++**:
   - Reemplazar `scanf` con `std::cin` o `std::getline` para evitar problemas de seguridad con el manejo de buffers.
   - Ejemplo:
     ```cpp
     std::string filename;
     std::cout << "Ingrese el nombre del archivo: ";
     std::getline(std::cin, filename);
     ```

3. **Automatización de manejo de recursos**:
   - Usar un enfoque RAII con `std::unique_ptr` o `std::ifstream` para asegurar el cierre del archivo:
     ```cpp
     std::ifstream file(filename, std::ios::binary);
     if (!file) {
         std::cerr << "Error: No se pudo abrir el archivo '" << filename << "'.\n";
         return;
     }
     std::string script((std::istreambuf_iterator<char>(file)), std::istreambuf_iterator<char>());
     ```

4. **Comentarios detallados**:
   - Agregar comentarios adicionales en partes críticas, como el manejo de errores o la lógica de lectura del archivo, para facilitar la comprensión del código.

---

### **Resumen**
Este ejercicio está bien desarrollado, con un diseño sólido y un manejo adecuado de errores. Las recomendaciones apuntan a mejorar la modularidad, adoptar prácticas modernas de C++ y hacer el código más legible y flexible para futuras expansiones. 

**Calificación final: 95/100.**
