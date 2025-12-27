# 5 Respuestas 
Basandonos en las lecturas aplicaremos el Principio de Inversión de Dependencias (DIP) y el patron mediator para reestructurar la comunicación entre acquisition service, inference service y audit service.

### Dependency Inversion Principle (DIP)

El problema actual es que el inference-service (alto nivel, lógica clínica) probablemente depende directamente de la implementación del acquisition-service (bajo nivel, drivers de sensores específicos), lo que crea un acoplamiento rígido. Si cambiamos el sensor, rompemos la inferencia. Una solucion con DIP seria que tanto los servicios de adquisición como los de inferencia dependerán de abstracciones (Contratos/Interfaces), no de implementaciones concretas.
Habria Abstracciones definidas IBioSignalStream. Define cómo se ven los datos, independientemente de si vienen de un sensor USB, Bluetooth o un archivo de prueba. Un beneficio de esto es que permite cambiar el hardware de adquisición sin tocar una sola línea de código del modelo de IA.


### Patrón Mediator

El problema actual seria la comunicacion "espagueti". Si acquisition service llama a inferenceservice y luego este llama a audit service, un fallo en la auditoria podria bloquear la adquisicion de datos vitales. Como solucion introducimos un componente central, el ClinicalOrchestrator (Mediator), que gestiona el flujo de trabajo. Los servicios no hablan entre ss, solo responden al Mediator.Esto nos traeria un benefico de desacopla la lógica. Si queremos añadir un paso de "Validación de Calidad de Señal" antes de la inferencia, solo modificamos el Mediator, no los servicios.