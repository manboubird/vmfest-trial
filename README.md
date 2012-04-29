# vmfest-trial

just a trial project for vmfest.

## Requirement

[Leiningen](https://github.com/technomancy/leiningen)
[vmfest v 0.2.3](https://github.com/tbatchelli/vmfest)

## Virtual box server setup

```
# virtual box version must be VirtualBox 4.0x
$ VBoxManage  -v
4.0.6r71344

# turn off authentication
$ VBoxManage setproperty websrvauthlibrary null

# start virtual box server
$ vboxwebsrv -t0
```

### vmfest setup

Clone project

```
$ git clone {REPO}
```

Clone image and create meta file as vmfest preparation.

```
$ mkdir -p ~/vmfest/models

# clone your vdi image dev-base.vdi
# UUID is produced when cloning vdi is completed.
$ VBoxManage clonehd /path/to/dev-base.vdi ~/vmfest/models/dev-base.vdi

# keep uuid in file.
$ echo "PRODUCED_UUID" > ~/vmfest/models/dev-base.vdi.uuid

# show hdds with uuid.
$ vboxmanage list hdds

# copy meta template file. The template file is for centOS 5.5(64bit). 
$ cp conf/dev-base.meta.tpl ~/vmfest/models/dev-base.meta

# edit uuid, username, password and sudo-password in dev-base.meta
$ vim ~/vmfest/models/dev-base.meta

```
The meta file is based on [pallet.compute.vmfest doc](http://palletops.com/pallet/api/0.6/pallet.compute.vmfest.html) .

Start REPL

```
$ lein deps
$ lein repl
```

Install models

```clojure
(use 'vmfest.manager)
(use 'vmfest.virtualbox.image)

;; create a connection to the virtual box server
(def my-server (server "http://localhost:18083"))

;; install vdi
(setup-model "file://{HOME_DIR}/vmfest/models/dev-base.vdi" my-server)

;; confirm the vdi is installed correctly.
(update-models)

;; define instance of image.
;; args of instance are 'server' 'displayed name on virtualbox' 'image' basic-config
(def my-machine (instance my-server "my-machine" :vmfest-dev-base :micro))

(start my-machine)
(pause my-machine)
(resume my-machine)

;; shut down OS
(stop my-machine) 

;; turns off the machine
(power-down my-machine)

;; destroy the machine. All files will be deleted (irrecoverable)
(destroy my-machine)


;; list currently defined machines
(pprint (map as-map (machines my-server)))


```

When you attach already crated machine


```clojure
(use 'vmfest.manager)
(use 'vmfest.virtualbox.image)
(def my-server (server "http://localhost:18083"))

;; "my-machine" is displayed name on virtualBox
(def my-machine (find-machine my-server "my-machine"))
(start my-machine)
```


## TODO

- move to [pallet-vmfest](https://github.com/pallet/pallet-vmfest)
 - allow to specify local network which are NAT and internal network(host-only network)
 - doc: [vmfest.clj](https://github.com/pallet/pallet-vmfest/blob/develop/src/pallet/compute/vmfest.clj)
  
Links
-----

- [VMFest](https://github.com/tbatchelli/vmfest)
- [VirtualBox](https://virtualbox.org/)
- [Pallet](http://palletops.com/)

## License

