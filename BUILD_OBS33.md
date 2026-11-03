# Compilación de Halsu plugins para OBS 33.0.1

## Qué incompatibilidad se ha corregido

OBS 33.0.1 quitó el modelo de carga de imágenes heredado basado en `gs_image_file` y lo sustituyó por `gs_image_file_ex` en `libobs/graphics/image-file.h`.

Los plugins afectados eran:

- [Halsu_HybridKeyer/plugin_main.cpp](Halsu_HybridKeyer/plugin_main.cpp)
- [Halsu_Relightwrap/plugin_main.cpp](Halsu_Relightwrap/plugin_main.cpp)

Ambos usaban estructuras y funciones antiguas como:

- `gs_image_file_t`
- `gs_image_file_init`
- `gs_image_file_free`
- `gs_image_file_init_texture`

La corrección aplicada es mínima y compatible con OBS 33: se añade una capa de compatibilidad que usa `gs_image_file_ex_t` y sus funciones equivalentes cuando `LIBOBS_API_MAJOR_VER >= 33`.

## Archivos modificados

- [Halsu_HybridKeyer/plugin_main.cpp](Halsu_HybridKeyer/plugin_main.cpp)
- [Halsu_Relightwrap/plugin_main.cpp](Halsu_Relightwrap/plugin_main.cpp)
- [.github/workflows/build-obs33.yml](.github/workflows/build-obs33.yml)

## Cómo ejecutar el workflow

1. Abre GitHub en tu fork.
2. Ve a la pestaña Actions.
3. Selecciona el workflow `Build OBS 33 plugins`.
4. Pulsa `Run workflow` o usa `workflow_dispatch` desde la interfaz.
5. La pipeline descargará la rama `33.0.1` de `obsproject/obs-studio`, compilará únicamente `libobs`, y después generará las DLL de los plugins.

## Cómo descargar las DLL compiladas

Cuando termine la ejecución del workflow:

1. Abre la ejecución del workflow.
2. En la sección Artifacts, descarga `halsu-plugins-obs33`.
3. El ZIP incluirá:
   - `obs-plugins/64bit/Halsu_HybridKeyer.dll`
   - `obs-plugins/64bit/Halsu_Relightwrap.dll`
   - `data/obs-plugins/Halsu_HybridKeyer/Halsu_HybridKeyer.effect`
   - `data/obs-plugins/Halsu_Relightwrap/Halsu_Relightwrap.effect`

## Instalación en OBS 33.0.1

1. Cierra OBS Studio.
2. Abre la carpeta de instalación de OBS.
3. Copia los archivos de `obs-plugins/64bit/` a la carpeta del mismo nombre del instalador.
4. Copia los archivos de `data/obs-plugins/.../` a la estructura de datos de OBS.
5. Vuelve a abrir OBS Studio y prueba el filtro correspondiente.

La estructura esperada es la misma que usa la distribución oficial de plugins de OBS:

- `obs-plugins/64bit/`
- `data/obs-plugins/<nombre-del-plugin>/`

## Qué pruebas faltan por confirmar

La validación real requiere una sesión de OBS 33.0.1 en Windows x64 con los plugins cargados en un entorno real. En esta máquina no se pudo ejecutar una compilación local completa ni una prueba real de carga, por lo que queda pendiente:

- cargar cada DLL con OBS 33.0.1
- probar ambos filtros con una escena real
- verificar la referencia, máscara y textura de ejemplo
- confirmar que no hay errores de contexto gráfico ni de textura al cambiar la ruta de imagen

La corrección es coherente con la API oficial de OBS 33, pero la confirmación final debe hacerse con una instalación real de OBS 33.0.1.
