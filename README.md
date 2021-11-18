# Malbait
Transcripción del Readme.rm de Malbait
Malbait 
Programa TCP / UDP Honeypot implementado en Perl 
LÉAME para malbait, 2/10/17 
¡Bienvenido a malbait, su sencillo y completo honeypot de perl! 
¡INICIO RÁPIDO! 
Para uso comercial, recomiendo ejecutar este programa en modo "-defaults" con privilegios de superusuario (sudo) en una computadora pequeña, barata y permanentemente encendida como una Raspberry Pi o incluso una antigua Asus EEE-PC conectada a la red que desee defender. Revise los archivos de registro con regularidad y si alguien viene husmeando, pronto lo sabrá. Esto es especialmente útil si tiene una red inalámbrica, aunque también recomiendo reforzar la seguridad en general si este es el caso. 
1.	Que es malbait 
2.	Que hace malbait 
3.	Protocolo de transporte 
4.	¿Hacktime? ¿Pelusa (fuzz)? 
5.	El servidor HTTP 
6.	Transblank 
7.	Noloop, no_multiplex y otros exóticos 
8.	Mensajes de error y depuración 
9.	Archivos de registro 
10.	Versiones futuras 
1.	Qué es malbait. 
Malbait es un honeypot o programa de "cebo de malware". Está diseñado para hacer perder el tiempo a los piratas informáticos y monitorear el tráfico hostil en Internet (o su LAN). 
2.	Que hace malbait 
Malbait crea una serie de servidores falsos en puertos TCP seleccionados o predeterminados y, si se seleccionan, puertos UDP. 
Si alguien o algo se conecta a ellos, su entrada se registrará en los archivos de registro. 
Estos se ejecutan como procesos en segundo plano, por lo que tendrá que apagar su computadora o usar el comando "kill" (o, mejor aún, "skill") del shell para deshacerse de ellos. 
Puede monitorearlos con netstat. Recomiendo: 
sudo netstat -anp | grep perl 

para ver lo que está escuchando y  
sudo skill -9 perl 

para matarlos cuando hayas tenido suficiente. 

Nota: Si usa install.sh para instalar este programa, tendrá que ejecutar 
sudo skill -9 malbait 
en lugar de sudo skill -9 perl,  aunque aún puede monitorearlo con: 
sudo netstat -anp | grep perl 

Alternativamente, puede simplemente apagar la máquina. ¡Obviamente, no use malbait mientras usa perl para cualquier otra cosa! Recomiendo una máquina "cebo" dedicada, ya sea algo pequeño como una Raspberry Pi que ejecute los puertos predeterminados, o una computadora grande que ejecute tantos puertos como pueda escuchar (preferiblemente todos). 

Malbait utiliza TCP de forma predeterminada, pero puede admitir UDP por sí solo y ambos se ejecutan en paralelo. 

Se recomienda MUY ENCARECIDAMENTE que utilice este programa en modo superusuario, ya sea invocando a gainroot (sudo gainroot) o sudo (sudo malbait). DEBE tener permisos de superusuario para abrir cualquiera de los primeros 1024 puertos. 

Se invoca así 
malbait -foo 

Si lo ejecuta sin parámetros, presentará un texto extenso que explica el uso con más detalle. Si desea comenzar "de inmediato", ejecute 
malbait -defaults 

Esto abrirá sus puertos predeterminados y creará servidores siempre que sea posible. 

Malbait puede crear servidores ficticios para Telnet, FTP, SMTP, POP3, BGP, HTTP (no brillantemente), TR-69 (no brillantemente), imap, systat, echo y el antiguo servidor ascii "time". De forma predeterminada, estos se abren en todos sus puertos predeterminados, pero puede especificar puertos para ellos. 

También puede abrir una variedad de puertos, es decir, 
malbait -puertos: 1-1024 

Abrirá los puertos 1-1024 (los puertos "conocidos" o "restringidos") y creará servidores falsos cuando corresponda. 

El uso de malbait solo está restringido por la cantidad de memoria que tenga. 
Las opciones 
-trans_proto: tcpudp 
y 
-ports: 
te dan el poder de ver cada puerto si tienes una máquina lo suficientemente fuerte; o bloquear su computadora si no lo hace. Encuentro que mi pequeño bloc de notas Lenovo x200 puede manejar alrededor de 6000 puertos antes de que empiece a volverse loco, pero su kilometraje puede variar. ¿Quieres ver cuán poderosa es REALMENTE tu nueva “máquina”? Malbait puede proporcionar un punto de referencia; personalmente, me encantaría ver qué sucede en una máquina en ejecución 
malbait -puertos: 1-65535 -trans_proto: tcpudp 

en una computadora muy poderosa: en mi experiencia, con solo el 10% de eso, definitivamente hay más tráfico UDP malicioso (principalmente idiotas que intentan estafar llamadas telefónicas gratuitas, parece), y telnet es una placa de Petri absoluta, pero me encantaría para ver un estudio del territorio inexplorado más allá, si alguien tiene los caballos de fuerza (¡y la capacidad y las p*** /ov*** para desactivar completamente su firewall!) 
3.	Protocolos de transporte 

Si intenta abrir, digamos, un servidor FTP en modo UDP, (malbait -proto: ftp -trans_proto: udp) abrirá los puertos predeterminados, ¡pero no obtendrá un servidor FTP simulado! UDP solo creará servidores para servicios compatibles con UDP, que son asciitime, systat, echo y los metaprotocolos especiales "hacktime" y "fuzz". 
4.	¿Hacktime?. ¿FUZZ (Pelusa)? ¿Qué te pasa? 
Hacktime es una especie de broma inspirada en la película "Kung Fury". Crea servidores de tiempo NTP, tanto de  la vieja escuela como de la nueva, y transmite todos los 1 o todos los FF, la idea es emular la fecha de renovación para Unix Millenium ("hacktime-2036") o un sistema recién iniciado (" hacktime-1900 "). Obviamente, el servidor NTP es mucho más complicado que eso y probablemente no funcionará, pero definitivamente confundirá a cualquier pirata informático que lo ataque pensando que es un servidor NTP. 

Fuzz es solo eso: un intento de meterse con los ataques automatizados arrojándoles basura al azar. De nuevo, esto es experimental y no necesariamente me lo tomo muy en serio, pero pensé que alguien podría encontrarlo útil para que permanezca. 
5.	El servidor HTTP 
El servidor HTTP busca un archivo llamado "webpage.html". Si no lo encuentra, genera una simple página 404 en su lugar. Nuevamente, no es brillante, este programa se trata más de ayudarlo a verificar los clientes exóticos y aleatorios que intentan conectarse con usted no de aburridas y viejas arañas web. Tenga en cuenta que si lo abre en un navegador web, verá muchas cadenas "HTTP / 1.1 200 OK" debajo del contenido; esto se debe a que no está diseñado para abrirse en un navegador web, está destinado a mantener en línea a los robots de rastreo web potencialmente maliciosos durante el mayor tiempo posible. No dude en reemplazar el archivo webpage.html predeterminado por el suyo, por supuesto; aunque puede usarlo, está diseñado para fines de demostración. 
6.	Transblank 
Las opciones -transblank le permiten anular la creación de un servidor falso. Entonces, si invocas 
malbait.pl -port: 23 -proto: transblank 
o 
malbait.pl -proto: telnet -transblank 

malbait transmitirá lo más parecido a una respuesta en blanco (un retorno de carraige ( return) único y solitario) a los clientes en lugar de crear un "servidor Telnet". 

7.	-noloop, -no_multiplex y otros exóticos 
Se han proporcionado varias opciones para los amantes de la informática exótica. 
-noloop: No se repite, es decir, mata a cada servidor después de una conexión. Si es un servidor UDP, lo mata después de que el cliente haya ingresado un "comando". 

-no_multiplexing: detiene la multiplexación del servidor TCP, es decir, el lanzamiento de servidores en un proceso secundario. Aunque puede ahorrar mucha memoria de esta manera, NO se recomienda: se sabe que nmap bloquea los servidores TCP de Malbait, y tenerlos en un proceso secundario significa que malbait puede recuperarse y registrar este y otros intentos de conexión maliciosos. Entonces, si usa la opción "-no_multiplexing", GARANTIZO que terminará con archivos temporales huérfanos esparcidos por toda su carpeta / directorio de trabajo como una explosión, y esto lo entristecerá mucho; también perderá servidores de forma permanente cuando se bloqueen. TL; DR: No use esta opción. 

-maxreq: nn Le permite generar potencialmente nn servidores TCP al mismo tiempo, por lo que puede (potencialmente) servir a tantos "clientes" a la vez. El valor predeterminado es 2. 

-noblocking: inicia los servidores en modo "sin bloqueo"; 
Esto no tiene mucho sentido, aunque podría hacernos menos vulnerables a la “mierda” de nmaps si se usa junto con -no_multiplexing. Pero, ¿por qué querrías hacer eso? Sin embargo, en serio, algunos clientes prefieren que no bloquees, así que si tienes problemas, prueba a usar esta opción. 

-timeout: nn Establece el tiempo de espera para los clientes en nn segundos. El valor predeterminado es 30, pero si recibe muchos ataques automatizados, es posible que desee reducirlo para no perderse el resto de malware que se conecta a su computadora. 

-report: filename.txt Le permite nombrar sus propios archivos de informes. 

-recvbuffer: nn: establece una variable interna que almacena recv (bytes recibidos) en el valor nn. El valor predeterminado es 1024, por lo que si desea consumir más de 1k a la vez (no es un deseo irrazonable), aquí es donde lo cambia. 

8.	Mensajes de error y depuración 
La salida de texto de malbait es un poco ... desordenada en este momento. Está lleno de mensajes de depuración e información de estado. Puede ignorarlo con seguridad a menos que sea un desarrollador o un masoquista. Los mensajes de error reales se registran en el archivo de registro. 

9.	Archivos de registro 
Malbait crea tres tipos de archivos de registro: un archivo CSV que le brinda una descripción general de todos los clientes que se han conectado a su máquina, un archivo de texto con datos y análisis más detallados y archivos temporales (con nombres como TEMP-16537) que serán borrado automáticamente ya que malbait abre / cierra puertos y crea / destruye "servidores". 
Lo siento por los archivos temporales, era la única forma de asegurar que TODO el tráfico fuera capturado, ¡incluso cuando intenta hacernos colisionar! Si ve alguno merodeando, no haga nada, simplemente actualice la página y probablemente desaparecerá. 
10.	Versiones futuras 
Si las personas muestran interés en este programa y me envían correos electrónicos / dinero / cartas de amor / amenazas, lo respaldaré con actualizaciones y nuevas funciones; por ejemplo, puedo agregar fácilmente compatibilidad con ICMP y tal vez también un mejor manejo del cliente web. ¡Me lo he pasado genial escribiendo esto y sé que me encantaría apoyarlo! Pero si no tengo noticias de la gente, asumiré que no es tan útil y pasaré a otras cosas. Entonces, si desea ver más funciones geniales, o desea que arregle el Annoying Bug X, ¡comuníquese con nosotros! 
Mi dirección de correo electrónico es batchmcnulty@protonmail.com La dirección de bitcoin para este programa (pista, pista) es 16P267RxgkLsEDVEKwLGsfnUCJ6bfgyWKR Gracias por leer y, por favor, ¡envíame tus Satoshis! 
