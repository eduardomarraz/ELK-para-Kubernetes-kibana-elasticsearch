### Configuración de ELK para Kubernetes/microservicios

Este repositorio contiene los archivos de configuración necesarios para desplegar un stack ELK (Elasticsearch, Logstash, Kibana) en un entorno de Kubernetes. A continuación, se explica el propósito de cada archivo de configuración:

- **elastic-service.yml**: Define el servicio para Elasticsearch.
- **elastic.yml**: Configura el despliegue de Elasticsearch como un StatefulSet.
- **filebeat-daemon-set.yml**: Despliega Filebeat como un DaemonSet para enviar logs a Logstash.
- **kibana.yml**: Despliega Kibana para la visualización de logs.
- **logstash-config.yml**: Configura Logstash para el formato de logs que Elasticsearch puede entender.
- **logstash-deployment.yml**: Despliega Logstash como un Deployment.
- **rbac.yml**: Define los roles y permisos necesarios (RBAC) para que los componentes de ELK accedan a recursos en Kubernetes.
- **web-deployment.yml**: Ejemplo de despliegue de una aplicación web para visualización de logs en Kibana.

### Despliegue

1. **RBAC (Role-Based Access Control)**

   ```bash
   kubectl apply -f rbac.yml
   ```

   - **Descripción**: Este comando crea una cuenta de servicio con los permisos necesarios para leer servicios, endpoints y namespaces.

2. **Elasticsearch**

   ```bash
   kubectl apply -f elastic.yml
   ```

   - **Descripción**: Este comando define la configuración y despliega un StatefulSet para Elasticsearch.

3. **Servicio para Elasticsearch**

   ```bash
   kubectl apply -f elastic-service.yml
   ```

   - **Descripción**: Este comando crea un servicio para exponer Elasticsearch dentro del clúster.

4. **Logstash**

   ```bash
   kubectl apply -f logstash-config.yml
   kubectl apply -f logstash-deployment.yml
   ```

   - **Descripción**: Estos comandos configuran Logstash para recibir, procesar y enviar logs a Elasticsearch.

5. **Filebeat**

   ```bash
   kubectl apply -f filebeat-daemon-set.yml
   ```

   - **Descripción**: Este comando despliega Filebeat como un DaemonSet para enviar logs a Logstash.

6. **Kibana**

   ```bash
   kubectl apply -f kibana.yml
   ```

   - **Descripción**: Este comando despliega Kibana para visualizar y analizar los logs almacenados en Elasticsearch.

### Despliegue Conjunto

Si deseas desplegar todos los recursos juntos después de haber corregido y verificado cada archivo por separado:

```bash
kubectl apply -f rbac.yml -f elastic.yml -f elastic-service.yml -f logstash-config.yml -f logstash-deployment.yml -f filebeat-daemon-set.yml -f kibana.yml
```

Esto aplicará todos los archivos YAML en secuencia para configurar todo el stack ELK en tu clúster Kubernetes.

### Verificación del Despliegue

Una vez desplegados todos los componentes, verifica que los pods estén en estado `Running`:

```bash
kubectl get pods --namespace kube-system
```

Si alguno de los pods no está funcionando correctamente, puedes verificar los eventos relacionados con ese pod específico:

```bash
kubectl describe pod <nombre-del-pod> --namespace kube-system
```

### Acceso a Kibana

Para acceder a Kibana desde Minikube, obtén la URL utilizando el comando:

```bash
minikube service kibana-logging -n kube-system
```

Esto debería proporcionarte una URL donde puedes acceder a Kibana desde tu navegador web.

Si `minikube service` sigue presentando problemas, puedes reenviar manualmente el puerto de Kibana:

```bash
kubectl port-forward service/kibana-logging 5601:5601 --namespace kube-system
```

Luego, accede a Kibana en `http://localhost:5601`.


Con estos pasos, deberías poder desplegar y verificar el stack ELK correctamente en tu entorno Kubernetes. Asegúrate de revisar los logs y estados de los recursos para abordar cualquier problema específico que pueda surgir durante el despliegue.

---

## Index Pattern
Cuando estemos dentro de Kibana realizamos los siguientes pasos:
Pagina oficial de [Elastic](https://www.elastic.co/guide/en/kibana/7.17/index-patterns.html)

1. **Acceder a Kibana**:
   - Abre tu navegador web y navega a la URL de Kibana. Normalmente es algo como `http://localhost:5601` si estás ejecutando Kibana localmente.

2. **Ir a la sección de "Management"**:
   - En el menú de la izquierda, busca y haz clic en "Management" (Gestión). Esta sección te permite configurar varios aspectos de Kibana.

3. **Crear un nuevo patrón de índice**:
   - Dentro de la sección de "Management", haz clic en "Index Patterns" (Patrones de Índice).
   - Luego, haz clic en el botón "Create index pattern" (Crear patrón de índice).

4. **Definir el patrón de índice**:
   - En el campo de "Index pattern" (Patrón de índice), ingresa el nombre del índice o el patrón de índices que deseas usar. Por ejemplo, si tus índices en Elasticsearch tienen nombres que comienzan con `logs-`, puedes ingresar `logs-*` para incluir todos los índices que coincidan con ese patrón.
   - Haz clic en "Next step" (Siguiente paso).

5. **Seleccionar el campo de tiempo (si aplica)**:
   - Si tus datos incluyen un campo de tiempo (timestamp), selecciona el campo que quieres usar para el filtrado y la visualización basados en el tiempo. Si no tienes un campo de tiempo, selecciona "I don’t want to use the Time Filter" (No quiero usar el filtro de tiempo).
   - Haz clic en "Create index pattern" (Crear patrón de índice).

6. **Explorar y visualizar datos**:
   - Una vez creado el patrón de índice, puedes ir a la sección de "Discover" (Descubrir) en el menú de la izquierda para explorar tus datos.
   - También puedes usar el patrón de índice en la sección de "Visualize" (Visualizar) para crear visualizaciones, o en "Dashboard" (Tablero) para crear tableros de control.

Siguiendo estos pasos, podrás configurar un patrón de índice en Kibana y comenzar a explorar y visualizar tus datos almacenados en Elasticsearch.
