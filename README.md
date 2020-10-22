# apollo-k8s

### 使用

```text

cd mysql/

helm install mysql-apollo . -f values.yaml

cd apollo-service

helm install  apollo-service . -f values.yaml


cd apollo-pottal

helm install apollo-service . -f values.yaml



```


### FAQ

- mysql 进入查询表是否安装
  
  ```text
  [root@node1 mysql]# helm  status mysql-apollo  -n db
NAME: mysql-apollo
LAST DEPLOYED: Wed Oct 21 16:45:46 2020
NAMESPACE: db
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Please be patient while the chart is being deployed

Tip:

  Watch the deployment status using the command: kubectl get pods -w --namespace db

Services:

  echo Master: mysql-apollo.db.svc.cluster.local:3306
  echo Slave:  mysql-apollo-slave.db.svc.cluster.local:3306

Administrator credentials:

  echo Username: root
  echo Password : $(kubectl get secret --namespace db mysql-apollo -o jsonpath="{.data.mysql-root-password}" | base64 --decode)

To connect to your database:

  1. Run a pod that you can use as a client:

      kubectl run mysql-apollo-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mysql:8.0.21-debian-10-r17 --namespace db --command -- bash

  2. To connect to master service (read/write):

      mysql -h mysql-apollo.db.svc.cluster.local -uroot -p my_database

  3. To connect to slave service (read-only):

      mysql -h mysql-apollo-slave.db.svc.cluster.local -uroot -p my_database

To upgrade this helm chart:

  1. Obtain the password as described on the 'Administrator credentials' section and set the 'root.password' parameter as shown below:

      ROOT_PASSWORD=$(kubectl get secret --namespace db mysql-apollo -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
      helm upgrade mysql-apollo bitnami/mysql --set root.password=$ROOT_PASSWORD
  ```
- 因为k8s的一些问题需要修改configMap的配置，用NodePort或者ingress的方式，不然找不到meta-server
  ```text
//修改configService
spring.datasource.url = jdbc:mysql://mysql-apollo.db:3306/ApolloConfigDB?characterEncoding=utf8
spring.datasource.username = root
spring.datasource.password = root
//修改configService
apollo.config-service.url = http://10.200.64.10:32180
apollo.admin-service.url = http://10.200.64.10:30631

// 需要修改apollo-portal下的apollo-env.properties
dev.meta=http://10.200.64.10:32180

  ```
