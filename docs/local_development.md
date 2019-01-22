# Kubernetes по-русски
## Как тестировать все локально?
- https://github.com/kinvolk/kubernetes-the-hard-way-vagrant
    - Лучший путь для того чтобы разобраться в основных компонетах
    - Не подходит для локальной разработки
    - Слишком много памяти и виртуальных машин, single node k8s получается убогонький
    - Бинарники ставятся "ручками" а не через apt
    
- docker-compose для локальной разработки проще:
    - ДА, но паралельно поддерживать docker-compose.yml и kubernetes/*.yml будет геморой
    - есть https://github.com/kubernetes/kompose, но это просто конвертер и лишнее звено
    - есть https://github.com/docker/compose-on-kubernetes, будет работать только на нативном docker (win10pro), не будет будет работать с cri-o
             
- https://github.com/Azure/draft
    - скрывают от разработчика процесс deployment в кластер, 
    - удлиняет цикл разработки, кроме kubernetes/*.yml придется поддерживать свой packs/*.yml и helm
    - нельзя multinode k8s
- https://github.com/windmilleng/tilt
    - меньше скрывают от разработчика процесс deployment в кластер, но более понятный конфиг чем у draft 

- https://github.com/kubernetes/minikube
    - крутая вещь, подходит всем, но нельзя local multi-node k8s https://github.com/kubernetes/minikube/issues/94

- https://github.com/kubernetes-sigs/kubeadm-dind-cluster
    - еще более крутая вещь, но у нее docker in docker ;( а я хочу cri-o 
    - и еще на старых OS - windows 7 / osx < 10.9 все равно будет через boot2docker + virtualbox 

                   
### Буду пилить свой Vagrantfile + public vagrantbox и single+multi node k8s?
- Запилил - https://github.com/Slach/vagrant-kubernetes, https://app.vagrantup.com/Slach/boxes/vagrant-kubernetes
- Внутри ubuntu 18.04 + cri-o + kubeadm + kube-router все ставим из apt, за основу взяты:
    - https://github.com/andygabby/bionic64-k8s 
    - cri-o - https://launchpad.net/~projectatomic/+archive/ubuntu/ppa
    - https://coreos.com/etcd/docs/latest/tuning.html - тюнинг ETCD hight latency 
- Авторизацией сервисов по SSL сертификатам? 
    - ca.pem и т.п. генерируется через kubeadm init, распостранение сертификатов через kubeadm join
    
- Как правильно проверить security настройки kubernetes
    - https://github.com/aquasecurity/kube-bench  
- Непонятно как делать private image registry 
    - пока буду использовать как public hub.docker.com
    - как делать "только подписанные образы?"
- Как делать docker build внутри k8s + cri-o и потом делать docker push ?
    - https://github.com/genuinetools/img
    - https://github.com/GoogleContainerTools/kaniko#pushing-to-different-registries
- Как опубликовать свой vagrant box на vagrant cloud:

    - https://www.vagrantup.com/docs/cli/cloud.html#cloud-publish
- Непонятно как делать secrets для "локальной разработки"
- придется поддерживать два Dockerfile на каждый application, один "для разработки", второй production + multi-stage?
    - как сделать localVolume и проброс файлов?
    - как сделать аналог docker-compose restart?
    - #### как делать livereload + debug
        - https://github.com/cortesi/modd для перезапуска сервисов "по изменению файловой системы"
        - https://github.com/solo-io/kubesquash
        - https://github.com/aylei/kubectl-debug
        #### IDE 
        - go https://blog.jetbrains.com/go/2018/04/30/debugging-containerized-go-applications/
        - python https://www.jetbrains.com/help/pycharm/remote-debugging-with-product.html
        - php xdebug
        #### CLI
        - python https://github.com/inducer/pudb
        - rust, go https://gdbgui.com/
        - https://github.com/snare/voltron
        #### WEB-IDE
        - https://www.theia-ide.org/