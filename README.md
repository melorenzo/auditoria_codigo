# Configuración inicial del proyecto

## 1. Crear estructura de carpetas
- Ejecutar como Administrador (si es necesario) `setup_project.bat`
- Se generarán todas las carpetas y archivos vacíos.

## 2. Inicializar repositorio Git y ramas
- Crear un repositorio vacío en GitHub (sin README, sin .gitignore).
- En tu computadora, dentro de la carpeta del proyecto:
  ```bash
  git init
  git remote add origin https://github.com/tuusuario/auditoria-codigo.git
  git add .
  git commit -m "chore: estructura inicial del proyecto"
  git push -u origin main
