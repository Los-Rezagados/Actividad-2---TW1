<div align="center">

# 🏫 Actividad 2 — Infraestructura y Desarrollo Web Avanzado
### Servidor Web U.E. Horizonte - Fase 2

[![Ubuntu](https://img.shields.io/badge/Ubuntu_24.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)](https://nginx.org/)
[![PHP](https://img.shields.io/badge/PHP_8.3-777BB4?style=for-the-badge&logo=php&logoColor=white)](https://www.php.net/)
[![Python](https://img.shields.io/badge/Python_3-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)

**Institución:** Universidad Privada Domingo Savio (UPDS)  
**Materia:** Tecnología Web I  
**Grupo:** LOS REZAGADOS  

</div>

---

## 📋 Tabla de Contenidos
1. [Introducción y Resumen Ejecutivo](#-introducción-y-resumen-ejecutivo)
2. [Fundamento Teórico Aplicado](#-fundamento-teórico-aplicado)
3. [Arquitectura del Sistema (Fase 2)](#-arquitectura-del-sistema-fase-2)
4. [Seguridad y Mejores Prácticas](#-seguridad-y-mejores-prácticas)
5. [Estructura del Repositorio](#-estructura-del-repositorio)
6. [Guía de Despliegue (Quick Start)](#-guía-de-despliegue-quick-start)
7. [Documentación Adjunta (PDFs)](#-documentación-adjunta-pdfs)
8. [Integrantes del Equipo](#-integrantes-del-equipo)

---

## 🎯 Introducción y Resumen Ejecutivo

Este repositorio representa la continuación y evolución del proyecto de la **U.E. Horizonte**, correspondiente a la **Actividad 2** (Laboratorios 5 al 8). Mientras que la primera fase se centró en el levantamiento del servidor, la configuración de Nginx y el despliegue de sitios estáticos y PHP, esta segunda fase profundiza en la persistencia de datos, la comunicación con bases de datos, el desarrollo de APIs más robustas y la implementación de medidas de seguridad avanzadas.

El entorno sigue estando orquestado mediante **Docker**, garantizando que todos los servicios (Servidor Web, Base de Datos, Backend) funcionen de manera aislada, reproducible y escalable.

---

## 📚 Fundamento Teórico Aplicado

Para el desarrollo de esta segunda etapa, se aplicaron conceptos avanzados de la ingeniería web:

### 1. Persistencia de Datos y Bases de Datos Relacionales
A diferencia de los datos estáticos o las variables de sesión temporales, la información crítica (como las notas de los estudiantes, asistencia o usuarios del portal) ahora requiere almacenamiento permanente. Se implementó una base de datos relacional (como MySQL/MariaDB), la cual organiza la información en tablas con relaciones lógicas, garantizando la integridad referencial y permitiendo consultas rápidas mediante SQL.

### 2. Conexión Backend-Base de Datos (PDO)
La comunicación entre el código PHP y la base de datos se realiza utilizando **PDO (PHP Data Objects)**. Esto es fundamental por dos razones:
*   **Seguridad:** Permite el uso de *Consultas Preparadas* (Prepared Statements), las cuales previenen ataques de **Inyección SQL**, separando el código SQL de los datos ingresados por el usuario.
*   **Flexibilidad:** Permite cambiar de gestor de base de datos (de MySQL a PostgreSQL, por ejemplo) sin reescribir el código.

### 3. Arquitectura de API RESTful
Las APIs (como la de Python o las nuevas rutas en PHP) se diseñaron siguiendo los principios REST. Esto significa que utilizan los métodos HTTP correctamente (`GET` para consultar, `POST` para crear, `PUT` para actualizar, `DELETE` para eliminar) y devuelven los datos estructurados en formato **JSON**, facilitando la comunicación con futuros frontends (como aplicaciones móviles o React).

### 4. Gestión de Variables de Entorno (.env)
Para evitar subir contraseñas de bases de datos o claves secretas al repositorio, se utilizan archivos de variables de entorno (`.env`). Docker y el código leen estas variables, manteniendo la seguridad del repositorio público y permitiendo que el sistema funcione tanto en desarrollo local como en un servidor de producción.

---

## 🏗️ Arquitectura del Sistema (Fase 2)

La arquitectura se ha expandido para incluir la capa de persistencia:

```mermaid
graph TD
    Client[Cliente / Navegador] -->|HTTP :8080| Nginx{Nginx Reverse Proxy}
    
    Nginx -->|Host: portal.horizonte| PHP[PHP-FPM 8.3]
    Nginx -->|Ruta: /api/| Python[API Python]
    
    PHP -->|PDO / Prepared Statements| DB[(Base de Datos<br/>MySQL / MariaDB)]
    Python -->|Consultas SQL| DB
    
    subgraph Red Docker
        Nginx
        PHP
        Python
        DB
    end
