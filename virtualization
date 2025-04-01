## Virtualization

### KVM/QEMU Commands
- `qemu-kvm -m 2048 -hda disk.img` - run virtual machine with 2GB RAM using disk.img
- `virsh list --all` - list all virtual machines (running and stopped)
- `virsh start VM_NAME` - start a virtual machine
- `virsh shutdown VM_NAME` - gracefully shutdown a VM
- `virsh destroy VM_NAME` - forcefully stop a VM (like pulling power cord)
- `virsh undefine VM_NAME` - remove VM configuration (delete VM)
- `virsh suspend VM_NAME` / `virsh resume VM_NAME` - pause/unpause VM
- `virsh setmaxmem VM_NAME 80M --config` - set maximum memory limit
- `virsh setmem VM_NAME 80M --config` - set current memory allocation
- `virsh edit VM_NAME` - edit XML configuration of a VM
- `virsh console VM_NAME` - connect to VM console

### Docker Commands
- `docker run -d -p 8080:80 nginx` - run nginx container in background, mapping port 8080 to container port 80
- `docker ps` / `docker ps -a` - list running containers / all containers
- `docker images` - list available images
- `docker pull ubuntu:22.04` - download ubuntu image with tag 22.04
- `docker exec -it container_id bash` - get interactive shell in running container
- `docker stop container_id` / `docker rm container_id` - stop/remove container
- `docker build -t myapp:latest .` - build image from Dockerfile in current directory
- `docker-compose up -d` - start all services defined in docker-compose.yml
- `docker volume create myvolume` / `docker network create mynetwork` - create volume/network
