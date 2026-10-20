<div align="center">

# 🛡️ Actividad 2 — Infraestructura Web Segura y DNS Avanzado
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
1. [Resumen Ejecutivo](#-resumen-ejecutivo)
2. [Arquitectura de la Infraestructura](#-arquitectura-de-la-infraestructura)
3. [Desglose Técnico (Labs 5 al 8)](#-desglose-técnico-labs-5-al-8)
4. [Seguridad y Hardening (Deep Dive)](#-seguridad-y-hardening-deep-dive)
5. [Estructura del Repositorio](#-estructura-del-repositorio)
6. [Documentación Adjunta](#-documentación-adjunta)
7. [Integrantes del Equipo](#-integrantes-del-equipo)

---

## 🎯 Resumen Ejecutivo

Este repositorio representa la culminación de la **Fase 2** del proyecto de infraestructura para la **U.E. Horizonte**. Mientras que la primera fase (Actividad 1) estableció las bases del servidor web y las aplicaciones, esta segunda fase se enfoca en la **seguridad de la capa de transporte, la auditoría de redes y el despliegue de una infraestructura DNS robusta**. 

Todo el ecosistema ha sido orquestado utilizando **Docker** para garantizar la portabilidad, desplegando una red interna que integra servidores web con TLS 1.3, servidores DNS autoritativos (BIND9) sincronizados de forma segura y un resolutor con filtrado de contenido y cifrado de consultas.

---

## 🏗️ Arquitectura de la Infraestructura

El sistema se compone de dos grandes bloques interconectados: el entorno Web Seguro y la Red DNS Interna.

```mermaid
graph TD
    %% Capa Web (Lab 5)
    Client[Cliente / Navegador] -->|HTTPS :8443| Nginx{Nginx Reverse Proxy<br>CA Propia / TLS 1.3}
    Nginx -->|Proxy / FastCGI| Apps[Portal PHP / API Python]
    
    %% Capa DNS (Labs 6, 7, 8)
    subgraph Red DNS Interna - 10.10.10.0/24
        ClienteDNS[Cliente DNS<br>10.10.10.50] -->|Consultas :53| Resolutor[Unbound Resolutor<br>10.10.10.4]
        
        Resolutor -->|Stub-Zone| NS1[ns1 BIND9 Primario<br>10.10.10.2]
        NS1 -.->|NOTIFY + AXFR TSIG| NS2[ns2 BIND9 Secundario<br>10.10.10.3]
        NS2 -.->|AXFR TSIG| NS1
        
        Resolutor -->|DoT :853| Internet((Cloudflare 1.1.1.1<br>Quad9 9.9.9.9))
        Resolutor -.->|RPZ Filtrado| DominiosMalos(Bloqueo de Phishing<br>y Apuestas)
    end
