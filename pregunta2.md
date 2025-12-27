# 2. Respuestas 
Para garantizar que el sistema pueda responder qué se desplegó, cuándo, por quién y con qué dependencias, se debe implementar la siguiente cadena inmutable que vincula el código fuente con el artefacto en ejecución:

Se implementa la **inmutabilidad con git**, el problema de "manifiestos no versionados" se resuelve forzando que la única fuente de verdad sea el repositorio Git, aprovechando su arquitectura basada en Árboles de Merkle.

Cada estado del código debe identificarse por su Hash SHA (SHA-1/SHA-256) único. Como se explican en las lecturas, Git modela el historial como un grafo acíclico dirigido (DAG) donde cada objeto (commit) se identifica por un hash de su contenido y estructura de árbol.

Para su implementacion se prohiben despliegues directos desde máquinas locales, cada cambio realisado debe de tener un commit.

En la **evidencia de version** se pone etiquetas firmadas. Para solucionar el problema de la "imagen sin tag" o reconstruida ambiguamente, se debe abandonar el uso de tags ligeros o latest. Se utilizan etiquetas anotadas y firmadas, las etiquetas anotadas son objetos completos en la base de datos de Git que contienen nombre del etiquetador, correo, fecha y mensaje, además de poder ser firmadas criptográficamente (GPG). De esta manera nos permite auditar las versiones publicadas asegurando no solo el punto en la historia, sino la autenticidad de quien certificó ese punto como una versión estable.

Para la **evidencia de autoria** se usan las firmas digitales para saber quién autorizó el código y el despliegue, se establece una cadena de confianza criptográfica, esto quiere decir que en el codigo usaremos commits firmados (GPG) ya que verificariamos la autenticidad e integridad de cada cambio. Para el **Artefacto o imagen** hacemos lo mismo firmamos el contenedor, en las lecturas se destaca la politica de "solo etiquetas firmadas", donde se asegura que los modulos consumidos son autenticos mediante verificación de firmas antes del uso.

Para la **evidencia de construccion y dependencias** se utiliza el ya mencionado SBOM que vendria a hacer un inventario estructurado de las dependencias y componentes, generado automaticamente durante el build, ademas de este tambien generariamos  SLSA para la generacion de metadatos de procedencia que documentan cómo, cuándo y quién creó el artefacto, esto vincula matemáticamente la imagen resultante con el Commit SHA exacto del que provino.

Finalmente la verificacion en **runtime** esto es para el cluster pruebe que version corrio, tenemos los **controles de admision** que es configurar el cluster (Kubernetes) para que rechace cualquier imagen que no tenga una firma válida y un attestation de procedencia vinculado al repositorio oficial. 