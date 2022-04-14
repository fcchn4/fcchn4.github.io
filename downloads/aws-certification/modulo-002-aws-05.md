# Módulo 2 - Amazon Virtual Private Cloud (VPC)

![](https://i.imgur.com/AAwcIAq.png)

En este módulo aprenderá sobre **Amazon Virtual Private Cloud** o **Amazon VPC**.

![](https://i.imgur.com/m6rfOAK.png)

La nube de **AWS** ofrece informática bajo demanda y pago por uso, así como servicios administrados; todos con acceso desde la Web. Estos servicios y recursos informáticos deben poder accederse mediante protocolos IP normales implementados con estructuras de red familiares. Los clientes deben cumplir con las prácticas recomendadas de redes, así como con los requisitos regulatorios y de organización. **Amazon Virtual Private Cloud** o **VPC**, es el servicio de redes de **AWS** que satisface sus requisitos de redes.

**Amazon VPC** le permite crear una red privada dentro de la nube de **AWS** que utiliza muchos de los mismos conceptos y construcciones que usa una red local, pero, como veremos más adelante, gran parte de la complejidad de la configuración de una red se ha eliminado sin sacrificar el control, la seguridad y la facilidad de uso. **Amazon VPC** también le proporciona un control absoluto de la configuración de la red. Los clientes pueden definir elementos de configuraciones de redes normales, como espacio de direcciones IP, subredes y tablas de direccionamiento. Esto le permite controlar lo que expone en internet y lo que aísla dentro de la **Amazon VPC**. Puede implementar su **Amazon VPC** de manera tal que forme capas de controles de seguridad en la red. Esto incluye el aislamiento de subredes, de definición de listas de control de acceso y la personalización de reglas de direccionamiento. Tiene control absoluto para permitir y denegar tanto el tráfico entrante como el saliente. Por último hay varios servicios de **AWS** que se implementan en su **Amazon VPC**, que luego se heredan y aprovechan de la seguridad que ha creado en la red en la nube. 

![](https://i.imgur.com/2UzO8b9.png)

**Amazon VPC** es un servicio fundamental de **AWS** y se integra a numerosos servicios de **AWS**. Por ejemplo las instancias de **Amazon Elastic Cloud Compute (Amazon EC2)** se implementan en su **Amazon VPC**. De forma similar, las instancias de la base de datos de **Amazon Relational Database Service (Amazon RDS)** se implementa en la **Amazon VPC**, donde la base de datos está protegida por la estructura de la red, tal como sucede en su red local. Comprender e implementar **Amazon VPC** le permitirá utilizar por completo otros servicios de **AWS**. 

**Amazon VPC** se construye sobre la infraestructura global de **AWS** de **regiones** y **zonas de disponibilidad** y le permite aprovechar fácilmente la **alta disponibilidad** proporcionada por la nube de **AWS**. Las **VPC** de Amazon residen en regiones y pueden abarcar varias zonas de disponibilidad. Cada cuenta de **AWS** puede crear varias **VPC**  que se pueden utilizar para separar entornos. Una **VPC** define un espacio de dirección IP que luego se divide en subredes. Estas subredes se implementan dentro de las zonas de disponibilidad que provocan que la **VPC** se expanda en otras zonas de disponibilidad.

![](https://i.imgur.com/WQacN4n.png)

Puede crear muchas subredes en una **VPC**, aunque se recomienda no crear tantas para limitar la complejidad de la topología de la red, pero esto depende totalmente de usted. Puede configurar tablas de enrutamiento para sus subredes con el objetivo de controlar el tráfico entre las redes e internet. De forma predeterminada todas las subsedes de una **VPC** pueden comunicarse entre sí. Las subredes suelen clasificarse en **públicas** o **privadas**. Las subredes **públicas** tienen acceso directo a internet mientras que las **privadas** no. Para que una subred sea pública, es necesario asociar un gateway de internet a la **VPC** y actualizar la tabla de enrutamiento de la subred pública a fin de enviar el tráfico que no es local a la gateway de internet. Las instancias de **Amazon EC2** también necesitan que se envíe una dirección IP pública a una gateway de internet.