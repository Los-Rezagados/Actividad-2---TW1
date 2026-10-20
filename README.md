<div align="center">

# 🏫 Actividad 2 — Infraestructura Web Segura y DNS Avanzado
### Servidor Web U.E. Horizonte - Fase 2 (Laboratorios 5 al 8)

[![Ubuntu](https://img.shields.io/badge/Ubuntu_24.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)](https://nginx.org/)
[![BIND9](https://img.shields.io/badge/BIND9-DNS-F15A24?style=for-the-badge&logo=isc&logoColor=white)](https://www.isc.org/bind/)
[![Unbound](https://img.shields.io/badge/Unbound-Resolutor-4A90E2?style=for-the-badge)](https://nlnetlabs.nl/projects/unbound/about/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)

**Institución:** Universidad Privada Domingo Savio (UPDS)  
**Materia:** Tecnología Web I  
**Grupo:** LOS REZAGADOS  

</div>

---

## 📋 Tabla de Contenidos
1. [Resumen del Proyecto](#-resumen-del-proyecto)
2. [Arquitectura de la Infraestructura](#-arquitectura-de-la-infraestructura)
3. [Desglose Técnico por Laboratorio](#-desglose-técnico-por-laboratorio)
4. [Seguridad y Hardening (Deep Dive)](#-seguridad-y-hardening-deep-dive)
5. [Estructura del Repositorio](#-estructura-del-repositorio)
6. [Documentación Adjunta (PDFs)](#-documentación-adjunta-pdfs)
7. [Integrantes del Equipo](#-integrantes-del-equipo)

---

## 🎯 Resumen del Proyecto

Este repositorio consolida la **Fase 2** del proyecto de infraestructura para la **U.E. Horizonte**. A través de un entorno completamente orquestado con **Docker**, se ha construido un ecosistema de red que abarca desde la seguridad criptográfica de las aplicaciones web hasta la administración de servidores DNS autoritativos y resolutores con altos estándares de seguridad.

El sistema garantiza que el portal de padres viaje cifrado por HTTPS, que la red interna del colegio esté protegida contra ataques de *Spoofing* (suplantación), y que la navegación de los estudiantes esté filtrada contra dominios maliciosos mediante políticas de DNS.

---

## 🏗️ Arquitectura de la Infraestructura

El proyecto se despliega sobre una red Docker aislada (`red_dns` en 10.10.10.0/24) que interconecta múltiples servicios críticos.

*(Recomendación: Sube una foto de tu diagrama de red a una carpeta llamada `img/` en tu repositorio y descomenta la siguiente línea para que se vea tu propio diagrama)*
<!-- ![Diagrama de Arquitectura](./img/diagrama-red.png) -->

```mermaid
graph TD
    %% Capa Web (Lab 5)
    Client[Navegador del Padre] -->|HTTPS :8443| Nginx{Nginx Web Server<br>CA Propia / TLS 1.3}
    Nginx -->|Proxy / FastCGI| Apps[Portal PHP / API Python]
    
    %% Capa DNS (Labs 6, 7, 8)
    subgraph Red DNS Interna (10.10.10.0/24)
        ClienteDNS[Cliente DNS<br>10.10.10.50] -->|Consultas :53| Resolutor[Unbound Resolutor<br>10.10.10.4]
        
        Resolutor -->|Stub-Zone| NS1[ns1 BIND9 Primario<br>10.10.10.2]
        NS1 -.->|NOTIFY + AXFR TSIG| NS2[ns2 BIND9 Secundario<br>10.10.10.3]
        NS2 -.->|AXFR TSIG| NS1
        
        Resolutor -->|DoT :853| Internet((Cloudflare 1.1.1.1<br>Quad9 9.9.9.9))
        Resolutor -.->|RPZ Filtrado| DominiosMalos(Bloqueo de Phishing<br>y Apuestas)
    end
