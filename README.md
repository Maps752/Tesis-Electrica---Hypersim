# Modelado, Control y Validación de Microrred Aislada con HYPERSIM (OPAL-RT)

Bienvenido al repositorio oficial del trabajo de grado: *"Modelado, Control y Validación con HYPERSIM - OPAL RT de una Microrred Aislada Basada 100% en Convertidores de Potencia"* (2026).

Este repositorio contiene los modelos de simulación, rutinas de cálculo paramétrico y documentación técnica necesarios para reproducir los escenarios dinámicos de una microrred conformada por un Sistema de Almacenamiento (BESS) en modo Grid-Forming, acoplado a generación fotovoltaica y eólica en modo Grid-Following.

## Contenido del Repositorio

* **Modelos_HYPERSIM**: Contiene el lienzo principal del proyecto y los subsistemas de los Recursos Energéticos Distribuidos (DERs).
* **Resultados_ScopeView**: Trazas dinámicas y transitorios extraídos de las 5 pruebas de validación descritas en el documento de tesis.

---

## Instructivo de Uso y Configuración

Para ejecutar este modelo correctamente en una estación de trabajo OPAL-RT, se deben seguir las siguientes directrices de configuración:

1. **Paso de Integración**: El resolvedor tipo trapezoidal implícito de HYPERSIM debe configurarse con un paso fijo de 50.1 µs.
2. **Frecuencias de Conmutación**: La portadora PWM de los inversores de almacenamiento y fotovoltaicos está ajustada a 1350 Hz, mientras que el convertidor del lado de la red (GSC) eólico opera a 2000 Hz.
3. **Gestión del Bus de Corriente Continua (DC Link)**: Para evitar el bloqueo por sobretensión en los algoritmos MPPT antes del acoplamiento a la red AC, verifique que los disyuntores de la red principal posean un retardo lógico (DelayOn) programado a 0.1 s.

---

## Aprendizajes Clave Utilizando HYPERSIM

Durante el desarrollo e implementación de este ecosistema de red aislada, se documentaron lecciones técnicas críticas sobre el uso de la simulación en tiempo real (EMT):

* **Mitigación de Colapsos Numéricos en el Arranque**: En simulaciones EMT que no cuentan con la inercia física de un generador tradicional, conectar todas las fuentes simultáneamente en el tiempo t=0 provoca divergencias en la matriz admitancia. El uso estratégico de bloques `DelayOn` para escalonar la conexión (permitiendo que el BESS pre-cargue la red y establezca el vector de fase antes de activar los algoritmos PLL del resto de inversores) es mandatario para evitar fallos matemáticos.
* **Manejo del Algoritmo MPPT en Vacío**: Se identificó que, si el inversor fotovoltaico no tiene un camino de descarga hacia la red AC (disyuntor abierto), el algoritmo MPPT empujará el ciclo de trabajo del convertidor Boost a su límite geométrico, saturando peligrosamente el bus DC. La simulación exige acoplar mecánicamente el sistema antes de iniciar la inyección real de irradiancia.
* **Técnica de Visualización "The Dot Trick" en ScopeView**: El paso de integración de 50.1 µs registra con alta fidelidad el rizado electromagnético de la conmutación PWM, lo que puede saturar visualmente las gráficas de potencia promedio y enmascarar la dinámica transitoria. La supresión de la interpolación lineal por un ploteo de puntos unitarios discretos resultó ser la mejor técnica para extraer tendencias limpias, sin alterar el rigor matemático del *solver* de tiempo real.
* **Sincronización de Topologías Back-to-Back**: Interconectar turbinas eólicas PMSG demanda un desacoplamiento estricto. HYPERSIM demostró ser excepcionalmente estable al permitir que el convertidor de máquina (MSC) estabilice el torque mientras el convertidor de red (GSC) regula el voltaje del enlace DC, todo bajo el soporte *Grid-Forming* dictado por el modelo electroquímico de las baterías.

---
**Autor:** Maria Paula Alarcón Perico  
**Institución:** Departamento de Ingeniería Eléctrica y Electrónica - Universidad de Los Andes