# Kubernetes-Jenkins-persistentvolume


"Prerequisites"

1) Kubernetes cluster in Google cloud

2) Login to google cloud shell


Create 4 yaml files

01.storageclass.config.yml  
02.pvc.config.yml  
03.jenkins.withvolume.deployment.yml  
04.jenkins.withvolume.service.yml



Create jenkins namespace
kubectl create ns jenkins

kubectl config set-context jenkins --cluster gke_kube
rnetes-poc-212109_us-central1-a_cluster-1 --user gke_kubernetes-poc-212109_us-central1-a_cluster-1 --namespace jenkins

Context "jenkins" created.

kubernetes-poc-212109)$ kubectl config use-context jenkins

Switched to context "jenkins".

kubectl get pods

No resources found.

 ls
 
01.storageclass.config.yml  02.pvc.config.yml  03.jenkins.withvolume.deployment.yml

kubectl apply -f 01.storageclass.config.yml

kubectl get StorageClass

NAME                 PROVISIONER            AGE

''gold                 kubernetes.io/gce-pd   49s"

standard (default)   kubernetes.io/gce-pd   3h

kubectl apply -f 02.pvc.config.yml

persistentvolumeclaim "jenkins-data" created

 kubectl get pvc
 
NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE

jenkins-data   Bound     pvc-6dbe53a2-9660-11e8-8dfe-42010a80013f   30Gi       RWO            gold           43s


Jenkins deployment

 kubectl apply -f 03.jenkins.withvolume.deployment.yml
 
deployment "jenkins-deployment" created


kubectl get pods

NAME                                  READY     STATUS              RESTARTS   AGE

jenkins-deployment-568d8f9cbb-tx552   0/1       ContainerCreating   0          25s

$ kubectl get deployments

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE

jenkins-deployment   1         1         1            0           31s



"Create ELB"

$ kubectl apply -f 04.jenkins.withvolume.service.yml

service "jenkins" created

$ kubectl get services

NAME      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE

jenkins   LoadBalancer   10.11.242.43   <pending>     8080:31266/TCP   12s
 
$ kubectl get services

NAME      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE

jenkins   LoadBalancer   10.11.242.43   <pending>     8080:31266/TCP   18s
 
$ kubectl get services

NAME      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)          AGE

jenkins   LoadBalancer   10.11.242.43   35.224.234.XXX   8080:31266/TCP   41s


Use external ip to connect to the jenkins server on port 8080

Create two jobs in jenkins in my case testJC and Test Job

Externally connected volume visible here


root@jenkins-deployment-568d8f9cbb-tx552:/dev# df -k

Filesystem     1K-blocks    Used Available Use% Mounted on

overlay         26615568 3544804  23054380  14% /

tmpfs            1897156       0   1897156   0% /dev

tmpfs            1897156       0   1897156   0% /sys/fs/cgroup

/dev/sdb        30832548  135792  29107508   1% /var/jenkins_home

/dev/sda1       26615568 3544804  23054380  14% /etc/hosts



root@jenkins-deployment-568d8f9cbb-tx552:/var/jenkins_home# ls -ltra

total 116

drwxr-xr-x  1 root root  4096 Dec  6  2017 ..

drwx------  2 root root 16384 Aug  2 14:30 lost+found

drwxr-xr-x  4 root root  4096 Aug  2 15:09 jobs

root@jenkins-deployment-568d8f9cbb-tx552:/var/jenkins_home# cd jobs

root@jenkins-deployment-568d8f9cbb-tx552:/var/jenkins_home/jobs# ls -ltra

total 16

drwxr-xr-x  3 root root 4096 Aug  2 15:07 Test Job

drwxr-xr-x  3 root root 4096 Aug  2 15:10 testJC


Kill the container and see next time you start a new jenkins deployment using the same volume gold .Check if the test job you created exists
