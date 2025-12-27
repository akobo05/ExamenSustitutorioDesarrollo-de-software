# 7. Respuestas 

### Diseño del Despliegue Final (Health-Grade)

**Docker Hardening**: Implementar User Namespaces (mapear UID 0 a usuario sin privilegios), eliminar capacidades (DROP ALL) y montar el sistema de archivos como Read-Only para evitar persistencia de amenazas.

**Kubernetes Zero-Trust:** NetworkPolicies, aqui pondriamos todo en denegar por defecto , PSS (Pod Security Standards) en esta parte se pondrian perfiles restristivos 

**Estrategia:** Aunque Canary me parece una de las mejores estrategias, debido a la naturaleza del proyecto no se puede aplicar por uqe expone a los pacientes ya que una version defecutosa seria un riesgo demasiado alto, por lo que se aplicaria **Blue-Green** ya que este si permite validacion en aislamiento.

**Alertas CLinicas**: Configurar alarmas por "Silencio de Datos" (sin señal >5s), "Latencia de Inferencia" (>200ms) y "Fallo de Firma en Auditoría".

**Evidencia de No repudio**: Generar un Manifiesto Firmado que contenga el git commit SHA + la imagen + Firma digital del que aprueba 