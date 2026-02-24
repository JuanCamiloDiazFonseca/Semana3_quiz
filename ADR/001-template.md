# ADR-001: Refactorización del módulo de autenticación por vulnerabilidades críticas de seguridad y violaciones de Clean Code

## Contexto

El sistema actual implementa un módulo básico de autenticación que permite el registro y login de usuarios mediante Spring Boot y acceso directo a PostgreSQL usando JDBC. Durante la auditoría del código se identificaron múltiples problemas de seguridad y mantenibilidad. Los hallazgos más críticos incluyen vulnerabilidades de SQL Injection debido a la concatenación directa de parámetros en las consultas, uso de hashing inseguro mediante MD5, exposición de información sensible en las respuestas del API y credenciales de base de datos hardcodeadas en el repositorio.

Estos problemas representan un riesgo alto para la seguridad del sistema, ya que pueden permitir bypass de autenticación, acceso no autorizado y exposición de datos sensibles. Adicionalmente, se observaron violaciones a principios de Clean Code y SOLID, especialmente el principio de responsabilidad única (SRP), debido a la mezcla de lógica de negocio, hashing y logging en una misma clase. La situación es urgente porque compromete la confidencialidad, integridad y mantenibilidad del sistema, afectando tanto a los usuarios como al equipo de desarrollo y al negocio.

## Decisión

1. Se reemplazará el uso de `Statement` por `PreparedStatement` en el repositorio para eliminar la vulnerabilidad de SQL Injection, garantizando la correcta parametrización de las consultas SQL.

2. Se eliminará el uso de MD5 como mecanismo de hashing de contraseñas y se adoptará un algoritmo seguro como BCrypt, que proporciona salting automático y mayor resistencia ante ataques de fuerza bruta.

3. Se evitará la exposición de información sensible en las respuestas del API, eliminando el retorno de hashes o datos internos del proceso de autenticación.

4. Se moverán las credenciales de base de datos fuera del código fuente hacia variables de entorno o archivos de configuración seguros, reduciendo el riesgo de filtración de secretos.

5. Se refactorizará la clase `AuthService` para cumplir con el principio SRP, separando responsabilidades como hashing, validaciones y logging en componentes independientes.

## Consecuencias

### Positivas

- Mejora significativa de la seguridad del sistema
- Eliminación de vulnerabilidades de SQL Injection
- Protección adecuada de contraseñas de usuario
- Reducción de exposición de datos sensibles
- Código más mantenible, legible y alineado con SOLID

### Riesgos / Costos

- Tiempo adicional requerido para refactorización
- Posibles regresiones si no se realizan pruebas completas
- Necesidad de migración de contraseñas existentes
- Ajustes en configuración y despliegue

## Alternativas consideradas

1. Reescribir completamente el módulo de autenticación  
   → Descartado debido al alto costo en tiempo y riesgo de introducir nuevos errores.

2. Corregir únicamente la vulnerabilidad de SQL Injection  
   → Descartado porque no soluciona problemas críticos como hashing inseguro, exposición de datos sensibles y violaciones de Clean Code.

3. Mantener MD5 y solo ocultar el hash en la respuesta  
   → Descartado porque MD5 es criptográficamente inseguro y no adecuado para contraseñas.