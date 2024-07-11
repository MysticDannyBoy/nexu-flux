# State-flux: egy state machine motor
![State-flux logo](State-flux%20logo%20small.png)

A State-flux egy Pythonban megírt véges-állapotú gépek lefuttatására képes applikáció. Célja a véges-állapotú gépek létrehozása, szerkesztése, lefuttatása és nyomon-követése.

A motor API pontokon keresztül kap inputot egy-egy állapot módosításra, lefuttatás alatt folyó gépenként, és egy webes felületen teszi lehetővé egyes gépek létrehozását, szerkesztését, kitörlését.

# Felépítés
## Docker image-ekből áll

Az egész úgy van megírva, hogy egy Docker compose stackben kell futtatni, az a legegyszerűbb.

A stack elemei:
- state-flux-engine:
	- egy Pythonban megírt state-machine motor
- state-flux-web:
	- egy webes felület a folyamatban lévő gépek kilistázására és állapotára, a gépek szerkesztésére, létrehozására és törlésére
- state-flux-db:
	- egy Redis-server instance arra, hogy az állapotok nyomon legyenek követve egy key-value párosítással

## state-flux-engine
### Python csomagok
Van egy pár Python csomag, amit ezen projekt alkalmazása érdekében lett megírva:

#### state-flux-scxml
Ez az `SCXML` formátumú fájlok ser-deser folyamataira lett megírva.

#### state-flux-transitions
Ez az [state-flux-scxml](#state-flux-scxml) csomagot használja fel a statikus fájlok használhatóvá tételére, és a [transitions](https://pypi.org/project/transitions/) csomagot a beolvasott state-machine-k futtatására.

## state-flux-web
Ez csak egy Flask app lesz, ami API pontokon keresztül kommunikál a [state-flux-engine](#state-flux-engine)-nel.

Ez fog majd többek között webes felületet biztosítani a state-machine-ek:
- létrehozására
- módosítására, frissítésére
- törlésére

Ehhez a `JointJS` framework-öt használnánk.

## state-flux-db
Egy redis-stack instance, ahol tárolva van egy-egy state-machine adott állapota. Ezzel kommunikál a [state-flux-engine](#state-flux-engine) a `10000`-s porton keresztül. Az adatbázis webfelülete megtekinthető a `20000`-es porton.

## docker-compose.yaml
```
name: state-flux
services:
	state-flux-engine:
		container_name: state-flux-engine
		image: mysticdannyboy/state-flux-engine:latest
		ports:
			- 8000:5000
	state-flux-web:
		container_name: state-flux-web
		image: mysticdannyboy/state-flux-web:latest
		ports:
			- 9000:5001
	state-flux-db:
		container_name: state-flux-db
		image: redis/redis-stack:latest
		ports:
			- 10000:6379
			- 20000:8001

```



# Futtatás

Jobb esetben ennyit kell majd csak csinálni a megadott `docker-compose.yaml` fájl mappájában:
```
docker-compose up -d
```
