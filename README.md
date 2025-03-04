# Data Masking en Azure

# 1. Investigación
## 1.1 Concepto de Data Masking y explica su importancia en la seguridad de datos.

Data Masking es una técnica de seguridad que oculta datos sensibles mediante la sustitución, modificación o enmascaramiento de la información real con valores ficticios pero realistas. Su objetivo es proteger datos confidenciales en entornos donde no se requiere el acceso a la información original, como en pruebas de software, análisis de datos o entrenamiento de personal.

La importancia del Data Masking radica en:

-Protección de la privacidad: Ayuda a cumplir con regulaciones como GDPR, HIPAA y PCI-DSS.

-Prevención de filtraciones: Reduce el riesgo de exposición de datos en entornos no seguros.

-Uso seguro en pruebas y desarrollo: Permite a los desarrolladores trabajar con datos sin comprometer la seguridad.

-Menor impacto en la producción: No afecta la estructura de la base de datos ni el rendimiento del sistema.

## 1.2 Tipos de Data Masking.

-Enmascaramiento Estático o Static Data Masking (SDM): Se aplica a una copia de la base de datos en la que los datos reales se sustituyen permanentemente por valores enmascarados. Se usa en entornos de prueba o desarrollo.

-Enmascaramiento Dinámico o Dynamic Data Masking (DDM): Oculta los datos en tiempo real a usuarios no autorizados, mostrando versiones enmascaradas sin modificar la base de datos original.

-On-the-fly Masking: Se aplica cuando los datos se mueven entre sistemas, asegurando que solo se transmitan valores enmascarados en tiempo real.

-Enmascaramiento Aleatorio: Este método reemplaza los datos originales con valores generados aleatoriamente que siguen el mismo formato y tipo de dato.

## 1.3 Aplicaciones comunes de Data Masking en empresas.

-Protección de datos en pruebas y desarrollo: Permite a los desarrolladores trabajar con datos realistas sin acceder a información sensible.

-Cumplimiento normativo: Ayuda a cumplir con leyes de privacidad y seguridad de datos.

-Seguridad en análisis de datos: Permite compartir información con analistas sin exponer datos sensibles.

-Reducción de riesgos en subcontratación: Evita que terceros accedan a información confidencial.

-Prevención de fraudes internos: Limita el acceso a datos críticos dentro de la empresa.

## 1.4 Data Masking en Azure SQL Database 

Azure ofrece una funcionalidad llamada "Dynamic Data Masking" que funciona así:

-Se configuran reglas de enmascaramiento directamente en la base de datos.
-Usuarios con privilegios especiales ven los datos completos.
-Usuarios estándar ven solo los datos enmascarados.

Ejemplo Práctico:

-Un número de tarjeta de crédito "1234-5678-9012-3456" se mostraría como "XXXX-XXXX-XXXX-3456"


# 2.Caso Práctico

## 2.1 Configura una instancia de Azure SQL Database con una tabla que contenga datos sensibles (por ejemplo, información personal de clientes).

CREATE TABLE Clientes (
    ID INT PRIMARY KEY,
    Nombre VARCHAR(100),
    CorreoElectronico VARCHAR(100),
    NumeroTarjeta VARCHAR(16),
    Salario DECIMAL(10,2)
);

INSERT INTO Clientes (ID, Nombre, CorreoElectronico, NumeroTarjeta, Salario)
VALUES
(1, 'Juan Pérez', 'juan.perez@email.com', '4111222233334444', 3500.50),
(2, 'María López', 'maria.lopez@email.com', '5555666677778888', 4200.75),
(3, 'Carlos Ramírez', 'carlos.ramirez@email.com', '1234567812345678', 5100.00),
(4, 'Ana Torres', 'ana.torres@email.com', '9876543298765432', 2800.25),
(5, 'Luis Fernández', 'luis.fernandez@email.com', '1111222233334444', 6000.80);


## 2.2 Implementa Dynamic Data Masking en al menos tres columnas de la tabla, utilizando diferentes tipos de máscaras

ALTER TABLE Clientes 
ALTER COLUMN CorreoElectronico 
ADD MASKED WITH (FUNCTION = 'email()');

ALTER TABLE Clientes 
ALTER COLUMN NumeroTarjeta 
ADD MASKED WITH (FUNCTION = 'partial(0, "XXXX-XXXX-XXXX-", 4)');

ALTER TABLE Clientes 
ALTER COLUMN Salario 
ADD MASKED WITH (FUNCTION = 'default()');

## 2.3 Visualización de datos antes y después del enmascaramiento

Crea dos usuarios con diferentes niveles de acceso:

-Usuario con permisos para ver datos sin enmascarar:

GRANT UNMASK TO usuario_admin;

-Usuario con acceso restringido:

CREATE USER usuario_limited WITHOUT LOGIN;
GRANT SELECT ON Clientes TO usuario_limited;

Se ejecuta la consulta desde ambos usuarios :

SELECT * FROM Clientes;

ID  | Nombre  | CorreoElectronico     | NumeroTarjeta          | Salario 
----|--------|----------------------|------------------------|--------
1   | Juan   | XXXX@XXXX.com         | XXXX-XXXX-XXXX-4444    | XXXX.XX
2   | María  | XXXX@XXXX.com         | XXXX-XXXX-XXXX-8888    | XXXX.XX
3   | Carlos | XXXX@XXXX.com         | XXXX-XXXX-XXXX-5678    | XXXX.XX
4   | Ana    | XXXX@XXXX.com         | XXXX-XXXX-XXXX-5432    | XXXX.XX
5   | Luis   | XXXX@XXXX.com         | XXXX-XXXX-XXXX-4444    | XXXX.XX

Mientras que el usuario administrador verá los datos reales.

ID  | Nombre  | CorreoElectronico         | NumeroTarjeta          | Salario  
----|--------|--------------------------|------------------------|--------
1   | Juan   | juan.perez@email.com      | 4111-2222-3333-4444    | 3500.50  
2   | María  | maria.lopez@email.com     | 5555-6666-7777-8888    | 4200.75  
3   | Carlos | carlos.ramirez@email.com  | 1234-5678-1234-5678    | 5100.00  
4   | Ana    | ana.torres@email.com      | 9876-5432-9876-5432    | 2800.25  
5   | Luis   | luis.fernandez@email.com  | 1111-2222-3333-4444    | 6000.80  


## 2.4 Documentación del proceso

Pasos seguidos:

-Creación de Azure SQL Database.

-Creación de una tabla con datos sensibles.

-Aplicación de Dynamic Data Masking en columnas clave.

-Creación de roles con diferentes permisos.

-Pruebas de acceso y verificación de los datos enmascarados.

Desafíos y soluciones:

.Problema: Configuración incorrecta de permisos de usuario.

.Solución: Revisar los privilegios con GRANT UNMASK y GRANT SELECT.

.Problema: No se aplicaban las máscaras correctamente.

.Solución: Confirmar que las columnas tengan el enmascaramiento configurado con sys.masked_columns.

Reflexión sobre data masking:

El Data Masking en Azure SQL Database proporciona una forma sencilla y efectiva de proteger información sensible sin afectar la estructura de la base de datos. Esto es especialmente útil en entornos de prueba, cumplimiento normativo y protección de datos de clientes. 
Además, permite definir niveles de acceso sin comprometer la seguridad general del sistema.
