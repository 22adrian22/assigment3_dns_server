#DNS_assigment3

En este proyecto se realiza la instalacion y configuracion
de un servidor DNS maestro y otro esclavo con una zona directa e
inversa.

#master

En la maquina maestro se realizan las siguientes configuraciones

En el archivo named se añade lo siguiente:
#activamos solo escucha IPv4

```
OPTIONS="-u bind -4"

```

En el archivo named.conf.local se añade lo siguiente:
#añadimos la zona directa e inversa que crearemos posteriormente
#definiendo el tipo (maestero y esclavo)
#definiendo la ruta de los archivos de configuracion de las zonas

```
#zona directa
zone "sistema.test" {
    type master;
    file "/var/lib/bind/db.sistema.test.dns";
};
#zona inversa
zone "57.168.192.in-addr.arpa" {
    type master;
    file "/var/lib/bind/db.192.168.57.dns";

```
En el archivo named.con.options se añade lo siguiente:
#lista de control de acceso para consultas recursivas
#filtrando segun la red

```
acl trusted {
	192.168.57.0/24;
	127.0.0.0/8;
};

```
#activamos la opcion dnssec-validation y comentamos la escucha a IPv6

```
dnssec-validation yes;

// listen-on-v6 { any; };

```

Se copia, duplica (uno por zona) y renombra el archivo db.empty
con los nombres configurados en el archivo named.conf.options

En el archivo de zona directa se añade lo siguiente:

```
$TTL	86400
@	IN	SOA	master root.sistema.test. (
		2010202401		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			  7200 )	; Negative Cache TTL
;
@	IN	NS	tierra.sistema.test. 
@	IN	NS	venus.sistema.test.
@	IN	NS	marte.sistema.test.

;
tierra 		IN A	192.168.57.103
venus 		IN A	192.168.57.102
marte 		IN A	192.168.57.104
mercurio	IN A	192.168.57.101

;eMail
@	IN	MX	10	marte.sistema.test.

;Alias
ns1		IN	CNAME	tierra.sistema.test.
ns2		IN	CNAME	venus.sistema.test.
mail	IN	CNAME	marte.sistema.test.

```

Para la zona inversa se añade lo siguiente:

```
$TTL	86400
@	IN	SOA	master.sisstema.test. root.sistema.test (
		2010202401		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			  7200 )	; Negative Cache TTL
;
@	IN	NS	tierra.sistema.test.
@	IN	NS	venus.sistema.test.

;PTR
103	IN	PTR	tierra.sistema.test.
102	IN	PTR	venus.sistema.test.
104	IN	PTR	marte.sistema.test.
101	IN	PTR	mercurio.sistema.test

```

En la maquina esclavo se realizan las siguientes configuraiones:

El archivo named y el archivo named.conf.options se mantienen
igual que en la maquina maestro

Los archivos de zona no son necesarios ya que se heredan de el 
maestro:

En el archivo named.conf .local se edita de la siguiente manera
para definir que el tipo es esclavo en ambas zonas y la maquina
maestro de la que deben heredar:

```

zone "sistema.test" {
    type slave;
    file "/var/lib/bind/db.sistema.test.dns";
    masters { 192.168.57.103; };
};

zone "57.168.192.in-addr.arpa" {
    type slave;
    file "/var/lib/bind/db.192.168.57.dns";
    masters { 192.168.57.103; };
};

```

Con estas configuraciones ya esta en funcionamiento el servidor DNS
maestro y el esclavo como se pide en la practica de manera funcional
para resolver sin fallos el archivo de test
