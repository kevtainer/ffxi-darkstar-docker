# ffxi-darkstar-docker

dockerfied darkstar allows you to quickly setup and deploy a containerized ffxi server.

two seperate services (server / db); utilizes docker volume to persist db data; supervisord for connect/game/search daemons; does not run as root.

server is based off of ubuntu:trusty, db is based off mysql:5.5 (why 5.5? there are null constraint issues in 5.6).

---

onbuild:
- db container shallow clones darkstar (stable), copies SQL into seed directory; cleans up
- server container installs dependencies; shallow clones darkstar (stable); compiles; cleans up

onstart:
- db container copies seed data to target; prepends a `use` statement; injects a zone_ip update script
- db container will run seed data if db defined as `$MYSQL_DATABASE` does not exist
- app server uses `sed` to inject environment configuration parameters

---

recommendations:

- use linux native or within a bridged linux VM. native osx/native windows or docker-machine may give you unexpected results
- optional: copy `.env.example` -> `.env`; modify to your needs, if you don't you'll see WARNINGS (but server should still work)
- optional: use an external volume mount for the database volume (nfs/filesystem)

instructions:

* install latest docker CE (https://store.docker.com/search?type=edition&offering=community)
* install latest docker-compose `$ pip install docker-compose`
* clone repo `git clone <ffxi-darkstar-docker>`
* cd into repo `cd ffxi-darkstar-docker`
* start services `docker-compose up`

---

admin:

* `docker-compose build` will force images to rebuild. to force a pull from darkstar `stable`, issue the build command with a `--no-cache` argument. 
* `docker-compose restart` will ... restart
* `docker-compose down -v` will nuke your database if you decide to forego the advice of using an external volume
* connect to `0.0.0.0:23055` to with your (MySQL) database tool of choice. use the credentials defined in `.env`; or the default `darkstar:darkstar`

---

usage:

see darkstar doc/wiki for how to actually use the server: https://wiki.dspt.info/index.php/Main_Page

services are exposed on the (typical) ports:

- `0.0.0.0:54230`
- `0.0.0.0:54230/udp`
- `0.0.0.0:54231`
- `0.0.0.0:54001`
- `0.0.0.0:54002`
- `0.0.0.0:23055` (MySQL DB)

---

considerations:

* moving login/map/search to separate containers. this all depends on the applications ability to resolve dns names. parameters like `login_data_ip` make me think otherwise.
* integration / build testing
* more runtime environment configuration
* ??? submit a PR