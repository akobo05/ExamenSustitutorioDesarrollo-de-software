# 6. Respuestas

### Plan de Pruebas de IaC y Plataforma (Health-Grade)

Nuestro objetivo es validar no solo que la infraestructura "suba", sino que cumpla con las restricciones legales y de privacidad antes, durante y despues del despliegue.

Como ya habia mencionado antes, para la seguridad de la IaC se implementa **el analisis estico y las politicas como codigo** 
Aqui es donde haremos el escaneo de secretos politcas OPA etc, me basare en mi proyecto Runner o nuestra PC5, para el desarrollo de este antes de cualquier despliegue, se ejecutan validaciones automáticas en el pipeline, similares a los checks definidos en los workflows de GitHub Actions de tu proyecto. En el escaneo de seguridad usamos los pre-commits donde una herramienta seria el  trufflehog para secretos y checkov para IaC. En nuestra PC5 establecemos las bases de seguridad (como no ejecutar en root). El plan de pruebas automatiza la verificación de estas reglas (por ejemplo, fallar si el Dockerfile usa USER root).
 La politica **OPA** tiene una regla todo contenedor debe ser efimero y siun privilegios, una manera de validar seria:
 Una política Rego impediría el despliegue si detecta montaje del socket de Docker (/var/run/docker.sock) en nodos clínicos, un riesgo que en mi PC5 mitiga usando runners efímeros pero que en salud es crítico bloquear.

 Ahora para las pruebas de **rollback** con datos sencibles. El rollback en salud es delicado porque no se pueden "borrar" datos de pacientes para volver atrás. Como estrategia podriamos pruebas en entornos staging persistentes, donde se haria la simulacion del delplegar , aplicar y ejecutar, en el caso de mi PC5 muestra cómo orquestar pasos secuenciales. Para el examen, añadiríamos un paso if: failure() que dispare un rollback job que restaure la versión previa de la imagen Docker sin tocar los volúmenes de datos persistentes (bases de datos cifradas).

 Para el End to End hay una validacion del flujo completo desde la ingesta hasta el diagnóstico, asegurando que no queden residuos de datos de prueba (PII). Se realizan pruebas de humo donde se verificaria  que el endpoint /health y /predict responden correctamente tras el despliegue. En la PC5 se implenta directamente este test donde mediante curl se espera un ok.

 Ya para terminar **el Teardown Seguro** , esto quiere decir que al terminar la prueba se debe destruir el entorno, en mi caso en nuestra PC5 incluimos el script cleanup_runner.sh que realiza una limpieza estandar. Para este contexto clinico evolucionariamos este script para que, además de borrar contenedores, ejecute un comando de borrado seguro (shred) sobre los volúmenes temporales usados en la prueba o destruya la clave de cifrado efímera usada para esos datos.