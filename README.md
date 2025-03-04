# Data Masking in Azure

## 1.1 Define el concepto de Data Masking y explica su importancia en la seguridad de datos.

Data Masking es una técnica de seguridad que oculta datos sensibles mediante la sustitución, modificación o enmascaramiento de la información real con valores ficticios pero realistas. Su objetivo es proteger datos confidenciales en entornos donde no se requiere el acceso a la información original, como en pruebas de software, análisis de datos o entrenamiento de personal.

La importancia del Data Masking radica en:

-Protección de la privacidad: Ayuda a cumplir con regulaciones como GDPR, HIPAA y PCI-DSS.

-Prevención de filtraciones: Reduce el riesgo de exposición de datos en entornos no seguros.

-Uso seguro en pruebas y desarrollo: Permite a los desarrolladores trabajar con datos sin comprometer la seguridad.

-Menor impacto en la producción: No afecta la estructura de la base de datos ni el rendimiento del sistema.

##1.2 Tipos de Data Masking

-Static Data Masking (SDM): Se aplica a una copia de la base de datos en la que los datos reales se sustituyen permanentemente por valores enmascarados. Se usa en entornos de prueba o desarrollo.

-Dynamic Data Masking (DDM): Oculta los datos en tiempo real a usuarios no autorizados, mostrando versiones enmascaradas sin modificar la base de datos original.

-On-the-fly Masking: Se aplica cuando los datos se mueven entre sistemas, asegurando que solo se transmitan valores enmascarados en tiempo real.
