# Población mundial · Zoomable Sunburst (D3 + World Bank)

Sunburst interactivo con la población total por **Mundo → Región → País** para el
último año disponible en los World Development Indicators del Banco Mundial.

## Estructura

```
├── index.html             # Gráfico D3 v7 (sin build, se carga D3 desde jsDelivr)
├── data/poblacion.json    # Árbol jerárquico generado por el script
├── scripts/build_data.py  # API World Bank -> JSON
└── requirements.txt
```

## 0. Crear el entorno virtual (una sola vez)

Un entorno virtual (`.venv`) es una carpeta con su propio Python y sus propias
librerías, separada del resto de la PC. Así cada proyecto tiene sus versiones de
pandas o requests sin pisarse con otros. La carpeta `.venv/` está en el
`.gitignore`: **no se sube a GitHub**, cada uno la crea en su máquina.

Todos los comandos se corren desde la carpeta raíz del repo (`poblacion-sunburst/`).

### Windows

PowerShell (la terminal por defecto de VS Code):

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

Si PowerShell responde *"la ejecución de scripts está deshabilitada en este sistema"*,
habilitalo una vez para tu usuario y volvé a activar:

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
.venv\Scripts\Activate.ps1
```

Si usás otra terminal:

| Terminal | Comando para activar |
|---|---|
| CMD (Símbolo del sistema) | `.venv\Scripts\activate.bat` |
| Git Bash | `source .venv/Scripts/activate` |

> Si `python` no se reconoce, probá con `py -m venv .venv`.

### macOS (y Linux)

```bash
python3 -m venv .venv
source .venv/bin/activate
```

> En Mac se usa `python3`: el comando `python` puede no existir.

### ¿Cómo sé si está activado?

El prompt de la terminal empieza con `(.venv)`:

```
(.venv) PS C:\Users\juan\poblacion-sunburst>
(.venv) juan@mac poblacion-sunburst %
```

Para salir del entorno: `deactivate`. La próxima vez que abras la terminal
no hace falta crearlo de nuevo: solo **activarlo**.

En VS Code: `Ctrl+Shift+P` (`Cmd+Shift+P` en Mac) → **Python: Select Interpreter**
→ elegir el que dice `.venv`. Así las terminales nuevas lo activan solas.

## 1. Generar los datos

Con el entorno activado:

```bash
pip install -r requirements.txt
python scripts/build_data.py
```

Para cambiar el idioma de los nombres o la carpeta de salida, editá las
constantes `IDIOMA`, `CARPETA_SALIDA` y `ARCHIVO_SALIDA` al principio del script.

El script:
1. Descarga `WB_WDI_SP_POP_TOTL` de la **Data360 API** (paginado de a 1000).
2. Descarga la clasificación de países de la **API v2** (`/v2/es/country`) y
   descarta los agregados (región `NA`) para no contar dos veces.
3. Toma el último año disponible, arma el árbol y lo guarda en `data/poblacion.json`.
4. Compara la suma de países contra el agregado `WLD` como control.

## 2. Probar localmente

```bash
python -m http.server
# abrir http://localhost:8000
```

> Abrir `index.html` con doble clic (`file://`) no funciona: el navegador bloquea
> la lectura del JSON.

## 3. Publicar en GitHub Pages

```bash
git init
git add .
git commit -m "Sunburst de población mundial"
git branch -M main
git remote add origin https://github.com/<usuario>/poblacion-sunburst.git
git push -u origin main
```

En GitHub: **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**.
El sitio queda en `https://<usuario>.github.io/poblacion-sunburst/`.

## Actualizar los datos

```bash
python scripts/build_data.py
git add data/poblacion.json
git commit -m "Actualiza datos de población"
git push
```

Fuente: Banco Mundial, World Development Indicators (SP.POP.TOTL).
Gráfico basado en [Zoomable Sunburst](https://observablehq.com/@d3/zoomable-sunburst) de Mike Bostock (ISC).
