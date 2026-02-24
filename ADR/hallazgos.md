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