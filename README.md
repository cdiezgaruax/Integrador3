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

