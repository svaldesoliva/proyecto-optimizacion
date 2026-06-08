# Proyecto de Optimización - ING200
Simón Valdés, Vicente Díaz.
## Estructura

```bash
proyecto-optimizacion/
├── data
│   ├── Datos.xlsx
│   ├── Operaciones_Permitidas.csv
│   └── Operaciones.csv
├── docs
│   ├── Indicaciones Generales.pdf
│   └── Proyecto.pdf
├── graphs
├── latex
│   ├── document.tex
│   └── TeX_Files/
│       ├── graphs/
│       └── logo.png
├── Proyecto.ipynb
├── README.md
└── requirements.txt
```

## Ejecución

Para poder ejecutar el código, se recomienda crear un entorno virtual para aislar las dependencias del proyecto.

Primero clonar el repositorio y navegar a el. 
```bash
git clone git@github.com:svaldesoliva/proyecto-optimizacion.git
cd proyecto-optimizacion
```
Luego crear un entorno virtual:
```bash
python3 -m venv .venv
source .venv/bin/activate #depende del shell pero este funciona para zsh/bash
```

Con el entorno virtual activado, instala las librerías necesarias ejecutando:
```bash
pip install -r requirements.txt
```

Finalmente, usando la mayoría de IDE's, podrás visualizar y ejecutar las celdas del script.  
En caso de no contar con soporte para visualizar este tipo de archivos, recomiendo usar *Jupyter Lab* para la visualización. Es un editor ligero que vive como dependencia en el proyecto.

Ejecuta el siguiente comando para instalar e iniciar Jupyter Lab. Esto abrirá una pestaña en tu navegador web con un editor:
```bash
python3 -m pip install jupyterlab
jupyter lab
```
