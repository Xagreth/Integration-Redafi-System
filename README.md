
# REDAFI Financial Integration System

Solución de Integración Empresarial (ESB) diseñada para **REDAFI** con el objetivo de centralizar, normalizar y enriquecer información financiera proveniente de múltiples fuentes heterogéneas.

Desarrollado sobre **Red Hat Fuse** y **Apache ActiveMQ** utilizando **Apache Camel** y Blueprint OSGi.

## Descripción del Proyecto

Este sistema resuelve la problemática de fragmentación de datos orquestando la ingesta desde cuatro orígenes distintos:
1.  **Archivos Planos (TXT)**: Sistemas legados.
2.  **Archivos JSON**: Microservicios modernos.
3.  **Archivos XML**: Proveedores bancarios.
4.  **API REST**: Transacciones en tiempo real.

El bus de integración procesa estos flujos mediante una arquitectura orientada a servicios (SOA), aplicando patrones de integración empresarial (EIP) para garantizar la consistencia y disponibilidad de los datos.

## Tecnologías Utilizadas

## Pila Técnica (Tech Stack)

### Entorno de Ejecución (Runtime)
* **Contenedor OSGi:** Red Hat Fuse 7.13 (Apache Karaf)
* **Framework de Integración:** Apache Camel 2.23 (Blueprint XML)
* **Message Broker:** Apache ActiveMQ 5.18 (Classic)
* **Servidor de Aplicaciones:** Jetty 9 (Integrado en Camel)
* **Lenguaje:** Java 11 (OpenJDK 11.0.29)

### Herramientas de Desarrollo
* **Sistema Operativo:** Linux openSUSE Tumbleweed
* **Editor de Código:** Kate 
* **Control de Versiones:** Git
* **Terminal:** Konsole
* **Pruebas de API:** cURL 

### Formatos de Datos
* **Intercambio:** JSON, XML, Texto Plano
* **Protocolos:** HTTP/REST, JMS (Java Message Service), File System

##  Patrones de Integración (EIP) Implementados

La solución implementa una arquitectura robusta basada en los siguientes patrones:

1.  **Message Endpoint:** Exposición de servicios HTTP mediante Jetty para recepción de datos vía API REST.
2.  **Normalizer:** Transformación de formatos dispares (XML, TXT, JSON) a un modelo canónico interno (JSON unificado).
3.  **Content Enricher:** Inyección automática de metadatos (Timestamp, Firmas de Auditoría) y validación contra base de datos interna.
4.  **Content Based Router:** Enrutamiento inteligente basado en la prioridad del origen (API REST -> Cola VIP).

##  Prerrequisitos

* Red Hat Fuse 7.x (o Apache ServiceMix)
* Apache ActiveMQ 5.18+
* Java 11

## ⚙ Instalación y Despliegue

1.  **Iniciar ActiveMQ:**
    Asegúrese de que el broker esté corriendo en el puerto `61616`.
    ```bash
    ./activemq console
    ```

2.  **Preparar Fuse:**
    Instalar las dependencias necesarias en la consola Karaf:
    ```bash
    feature:install camel-activemq camel-jetty camel-jackson
    ```

3.  **Despliegue (Hot Deployment):**
    Copie el archivo de contexto a la carpeta `deploy` del contenedor:
    ```bash
    cp src/main/resources/OSGI-INF/blueprint/redafi-context.xml $FUSE_HOME/deploy/
    ```

##  Pruebas de Uso

El sistema monitorea automáticamente las carpetas de entrada y el puerto HTTP `9999`.

### 1. Ingesta vía API REST (Alta Prioridad)
```bash
curl -X POST -H "Content-Type: text/plain" -d "Pago Express #999" http://localhost:9999/api/redafi/datos
