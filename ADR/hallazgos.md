# Hallazgos de Auditoría

| # | Descripción del problema | Archivo | Línea aprox. | Principio violado | Riesgo |
|---|---------------------------|----------|-------------|------------------|--------|
| 1 | Concatenación directa de SQL usando parámetros del usuario (`username`) | UserRepository.java | 17–22 | Seguridad — SQL Injection | Alto |
| 2 | Uso de `Statement` en lugar de `PreparedStatement` | UserRepository.java | 18 / 33 | Seguridad — SQL Injection | Alto |
| 3 | Credenciales de base de datos hardcodeadas en código fuente | UserRepository.java | 10–12 | Seguridad — Exposición de secretos | Alto |
| 4 | Uso de MD5 para hashing de contraseñas (algoritmo inseguro) | AuthService.java | 63 | Seguridad — Hashing débil | Alto |
| 5 | Exposición de información sensible en la respuesta del login (`hash`) | AuthService.java | 25 / 31 | Seguridad — Exposición de datos sensibles | Alto |
| 6 | Clase `User` con atributos públicos sin encapsulación | User.java | 3–7 | Clean Code — Encapsulación / SOLID (SRP) | Medio |
| 7 | Nombres de variables no descriptivos (`s`, `r`, `c`, `x`, `hp`) | Varias clases | Varias | Clean Code — Naming | Bajo |
| 8 | Comentarios redundantes que no agregan valor real | AuthService.java | 15 / 37 | Clean Code — Comentarios innecesarios | Bajo |
| 9 | Violación de SRP: AuthService mezcla hashing, lógica y logging | AuthService.java | General | SOLID — SRP | Medio |
|10 | Conexiones JDBC sin cierre explícito (posible fuga de recursos) | UserRepository.java | General | Buenas prácticas — Gestión de recursos | Medio |

# Resultados de Pruebas Funcionales

## 🧪 Prueba 1 — Login válido

**Request**

POST /login?u=admin&p=12345

**Respuesta**

{
  "ok": true,
  "user": "admin",
  "hash": "827ccb0eea8a706c4c34a16891f84e7b"
}

**Análisis**

La respuesta incluye el campo `hash`, que corresponde al MD5 de la contraseña ingresada.

**Problema de seguridad**

El hash de la contraseña es información sensible y no debería enviarse al cliente. 
Exponer hashes puede facilitar ataques de fuerza bruta, análisis de credenciales o reutilización de información sensible.

**Conclusión**

Existe una violación del principio de mínima exposición de datos sensibles.

**Riesgo: Alto**

## 🧪 Prueba 2 — SQL Injection

**Request**

POST /login?u=admin'--&p=cualquiercosa

**Respuesta**

{
  "ok": false,
  "hash": "f73862908453012d17eb1d60240d95d1"
}

**Análisis**

Aunque el login fue rechazado, la entrada maliciosa modifica la consulta SQL debido a la concatenación directa del parámetro `u`.

El payload `'--` introduce un comentario SQL que altera la estructura del query original.

**Problema de seguridad**

El sistema es vulnerable a SQL Injection porque construye consultas mediante concatenación de strings y usa `Statement` en lugar de `PreparedStatement`.

En un entorno real, esto podría permitir:

- Bypass de autenticación
- Acceso no autorizado
- Manipulación de datos
- Compromiso de la base de datos

**Conclusión**

Existe una vulnerabilidad crítica de SQL Injection.

**Riesgo: Crítico**

## 🧪 Prueba 3 — Registro con contraseña débil

**Requests**

POST /register?u=test&p=123&e=test@test.com
POST /register?u=test&p=1234&e=test@test.com

**Respuestas**

Para p=123

{
  "ok": false
}

Para p=1234

{
  "ok": true,
  "user": "test"
}

**Análisis**

La contraseña `123` fue rechazada, mientras que `1234` fue aceptada.

La validación implementada únicamente verifica que la longitud de la contraseña sea mayor a 3 caracteres.

**Problema de seguridad**

La validación es insuficiente y no garantiza contraseñas seguras. 
No se aplican reglas de complejidad, caracteres especiales ni políticas de seguridad adecuadas.

**Conclusión**

La política de contraseñas es débil y representa un riesgo de seguridad.

**Riesgo: Medio**

