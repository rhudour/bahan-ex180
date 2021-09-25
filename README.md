# bahan-ex180

## [**install podman**]
	sudo yum install podman
	sudo podman -v
	konfig registries >> /etc/containers/registries.conf
	konfig cni >> /etc/cni/net.d/87-podman-bridge.conflist
#
## [**run container**]
	[root@workstation do180-practice]#
	[root@workstation do180-practice]#  podman run --name mysql-basic -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 -d rhel8/mysql-80
	Trying to pull registry.do180.lab:5000/rhel8/mysql-80...
	Trying to pull registry.redhat.io/rhel8/mysql-80...
	Getting image source signatures
	Copying blob 1c176e0fbe37 done
	Copying blob 356f18f3a935 done
	Copying blob 359fed170a21 done
	Copying blob 296e14ee2414 done
	Copying config 7d3ef14d6a done
	Writing manifest to image destination
	Storing signatures
	087eefc6dd2e2df012587b8dab272982ead8027348bf90855c15cba2172064f8
	[root@workstation do180-practice]#
	[root@workstation vagrant]# podman ps
	CONTAINER ID  IMAGE                                     COMMAND     CREATED       STATUS           PORTS  NAMES
	087eefc6dd2e  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld  20 hours ago  Up 20 hours ago         mysql-basic
	[root@workstation vagrant]#
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman exec -it mysql-basic /bin/sh
	sh-4.4$ mysql -uroot
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 8
	Server version: 8.0.21 Source distribution

	Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> show databases;
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| items              |
	| mysql              |
	| performance_schema |
	| sys                |
	+--------------------+
	5 rows in set (0.31 sec)

	mysql> use items;
	Database changed
	mysql> CREATE TABLE Projects (id int(11) NOT NULL, name varchar(255) DEFAULT NULL, code varchar(255) DEFAULT NULL, PRIMARY KEY (id));
	Query OK, 0 rows affected, 1 warning (0.15 sec)

	mysql> show tables;
	+-----------------+
	| Tables_in_items |
	+-----------------+
	| Projects        |
	+-----------------+
	1 row in set (0.04 sec)

	mysql> insert into Projects (id, name, code) values (1, 'bahan', 'test180');
	Query OK, 1 row affected (0.15 sec)

	mysql> select * from Projects;
	+----+-------+---------+
	| id | name  | code    |
	+----+-------+---------+
	|  1 | bahan | test180 |
	+----+-------+---------+
	1 row in set (0.01 sec)

	mysql> exit
	Bye
	sh-4.4$ exit
	exit
	[root@workstation vagrant]#
	[root@workstation vagrant]#
#
## [**run container with persistent storage**]
	[root@workstation vagrant]# mkdir -pv /mysql
	mkdir: created directory ‘/mysql’
	[root@workstation vagrant]# ls /
	bin  boot  dev  dockerfiles  etc  home  lib  lib64  media  mnt  mysql  opt  proc  root  run  sbin  sql  srv  sys  tmp  usr  vagrant  var  web
	[root@workstation vagrant]# 
	[root@workstation vagrant]# 
	[root@workstation vagrant]# podman run --name persist-db -d -v /mysql:/var/lib/mysql/data -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 rhel8/mysql-80
	9bc35c172d5a1592de6f8145827eab316fe2bd4f5117a99d5917c4dbdc824784
	[root@workstation vagrant]# podman ps
	CONTAINER ID  IMAGE                                     COMMAND     CREATED        STATUS             PORTS  NAMES
	9bc35c172d5a  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld  4 seconds ago  Up 3 seconds ago          persist-db
	087eefc6dd2e  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld  3 days ago     Up 56 minutes ago         mysql-basic
	[root@workstation vagrant]#
	[root@workstation vagrant]#
	[root@workstation vagrant]# ls -l /mysql/
	total 94664
	-rw-r-----. 1 27 27        2 Sep 23 04:12 9bc35c172d5a.pid
	-rw-r-----. 1 27 27       56 Sep 23 04:12 auto.cnf
	-rw-r-----. 1 27 27     3132 Sep 23 04:12 binlog.000001
	-rw-r-----. 1 27 27      156 Sep 23 04:12 binlog.000002
	-rw-r-----. 1 27 27       32 Sep 23 04:12 binlog.index
	-rw-------. 1 27 27     1676 Sep 23 04:12 ca-key.pem
	-rw-r--r--. 1 27 27     1112 Sep 23 04:12 ca.pem
	-rw-r--r--. 1 27 27     1112 Sep 23 04:12 client-cert.pem
	-rw-------. 1 27 27     1680 Sep 23 04:12 client-key.pem
	-rw-r-----. 1 27 27   196608 Sep 23 04:14 #ib_16384_0.dblwr
	-rw-r-----. 1 27 27  8585216 Sep 23 04:12 #ib_16384_1.dblwr
	-rw-r-----. 1 27 27     3612 Sep 23 04:12 ib_buffer_pool
	-rw-r-----. 1 27 27 12582912 Sep 23 04:12 ibdata1
	-rw-r-----. 1 27 27  8388608 Sep 23 04:14 ib_logfile0
	-rw-r-----. 1 27 27  8388608 Sep 23 04:12 ib_logfile1
	-rw-r-----. 1 27 27 12582912 Sep 23 04:12 ibtmp1
	drwxr-x---. 2 27 27      187 Sep 23 04:12 #innodb_temp
	drwxr-x---. 2 27 27        6 Sep 23 04:12 items
	drwxr-x---. 2 27 27      143 Sep 23 04:12 mysql
	-rw-r-----. 1 27 27 25165824 Sep 23 04:12 mysql.ibd
	-rw-r--r--. 1 27 27        7 Sep 23 04:12 mysql_upgrade_info
	drwxr-x---. 2 27 27     8192 Sep 23 04:12 performance_schema
	-rw-------. 1 27 27     1680 Sep 23 04:12 private_key.pem
	-rw-r--r--. 1 27 27      452 Sep 23 04:12 public_key.pem
	-rw-r--r--. 1 27 27     1112 Sep 23 04:12 server-cert.pem
	-rw-------. 1 27 27     1680 Sep 23 04:12 server-key.pem
	drwxr-x---. 2 27 27       28 Sep 23 04:12 sys
	-rw-r-----. 1 27 27 10485760 Sep 23 04:14 undo_001
	-rw-r-----. 1 27 27 10485760 Sep 23 04:14 undo_002
	[root@workstation vagrant]#
#
## [**port mapping, commit, tag & push container image to registries**]
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman run --name my-apache-app -p 8080:80 -d docker.io/httpd
	a2ff324e93220f9f7d33b43ac4bc1437b4c58c6fd9693a709d09c57b5cfd9f94
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman ps
	CONTAINER ID  IMAGE                                     COMMAND           CREATED        STATUS            PORTS                 NAMES
	a2ff324e9322  docker.io/library/httpd:latest            httpd-foreground  9 seconds ago  Up 5 seconds ago  0.0.0.0:8080->80/tcp  my-apache-app
	9bc35c172d5a  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld        5 hours ago    Up 5 hours ago                          persist-db
	087eefc6dd2e  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld        4 days ago     Up 6 hours ago                          mysql-basic
	[root@workstation vagrant]#
	[root@workstation vagrant]# curl localhost:8080
	hi from container
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman ps
	CONTAINER ID  IMAGE                                     COMMAND           CREATED        STATUS            PORTS                 NAMES
	a2ff324e9322  docker.io/library/httpd:latest            httpd-foreground  5 minutes ago  Up 5 minutes ago  0.0.0.0:8080->80/tcp  my-apache-app
	9bc35c172d5a  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld        5 hours ago    Up 5 hours ago                          persist-db
	087eefc6dd2e  registry.redhat.io/rhel8/mysql-80:latest  run-mysqld        4 days ago     Up 6 hours ago                          mysql-basic
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman stop my-apache-app
	a2ff324e93220f9f7d33b43ac4bc1437b4c58c6fd9693a709d09c57b5cfd9f94
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman commit my-apache-app httpdku
	Getting image source signatures
	Copying blob d000633a5681 skipped: already exists
	Copying blob 2136d1b3a4af skipped: already exists
	Copying blob 3453c54913b8 skipped: already exists
	Copying blob 076616e79830 skipped: already exists
	Copying blob 8e72f30b4bc9 skipped: already exists
	Copying blob 13d424737bfc done
	Copying config 255d240a96 done
	Writing manifest to image destination
	Storing signatures
	255d240a96a29d3a4a65dac276eba0db97138df60a0eb42128afc25b7ad21107
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman images
	REPOSITORY                          TAG          IMAGE ID       CREATED          SIZE
	localhost/httpdku                   latest       255d240a96a2   17 seconds ago   142 MB
	docker.io/library/httpd             latest       5ebe6e00baf9   37 hours ago     142 MB
	localhost/skeeter                   1.0          40917d4414a2   10 days ago      391 MB
	quay.io/feby_ichsan/duff-nginx      1.0          a4a391cc3e1d   10 days ago      569 MB
	registry:5000/httpd                 twerks       4e857675bd6d   10 days ago      57.1 MB
	registry.do180.lab:5000/mariadb     latest       6b01262bc780   3 weeks ago      416 MB
	registry.do180.lab:5000/httpd       2.4-alpine   5a4f32436238   3 weeks ago      57.1 MB
	registry.do180.lab:5000/httpd       latest       c8ca530172a8   5 weeks ago      142 MB
	registry.redhat.io/rhel8/mysql-80   latest       7d3ef14d6a15   7 weeks ago      611 MB
	docker.io/library/centos            8            300e315adb2f   9 months ago     217 MB
	docker.io/library/centos            7            8652b9f0cb4c   10 months ago    212 MB
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman tag httpdku docker.io/voidarise1117/httpdku:v1
	[root@workstation vagrant]#
	[root@workstation vagrant]# podman images
	REPOSITORY                          TAG          IMAGE ID       CREATED         SIZE
	localhost/httpdku                   latest       255d240a96a2   3 minutes ago   142 MB
	docker.io/voidarise1117/httpdku     v1           255d240a96a2   3 minutes ago   142 MB
	docker.io/library/httpd             latest       5ebe6e00baf9   37 hours ago    142 MB
	localhost/skeeter                   1.0          40917d4414a2   10 days ago     391 MB
	quay.io/feby_ichsan/duff-nginx      1.0          a4a391cc3e1d   10 days ago     569 MB
	registry:5000/httpd                 twerks       4e857675bd6d   10 days ago     57.1 MB
	registry.do180.lab:5000/mariadb     latest       6b01262bc780   3 weeks ago     416 MB
	registry.do180.lab:5000/httpd       2.4-alpine   5a4f32436238   3 weeks ago     57.1 MB
	registry.do180.lab:5000/httpd       latest       c8ca530172a8   5 weeks ago     142 MB
	registry.redhat.io/rhel8/mysql-80   latest       7d3ef14d6a15   7 weeks ago     611 MB
	docker.io/library/centos            8            300e315adb2f   9 months ago    217 MB
	docker.io/library/centos            7            8652b9f0cb4c   10 months ago   212 MB
	[root@workstation vagrant]#
	[root@workstation vagrant]#
#
## [**build image**]
	[root@workstation s2-movie-info-service]# vi Dockerfile_t
	[root@workstation s2-movie-info-service]#
	[root@workstation s2-movie-info-service]# podman images
	REPOSITORY                          TAG          IMAGE ID       CREATED         SIZE
	localhost/httpdku                   latest       255d240a96a2   2 days ago      142 MB
	docker.io/voidarise1117/httpdku     v1           255d240a96a2   2 days ago      142 MB
	docker.io/library/httpd             latest       5ebe6e00baf9   3 days ago      142 MB
	localhost/skeeter                   1.0          40917d4414a2   12 days ago     391 MB
	quay.io/feby_ichsan/duff-nginx      1.0          a4a391cc3e1d   12 days ago     569 MB
	registry:5000/httpd                 twerks       4e857675bd6d   12 days ago     57.1 MB
	registry.do180.lab:5000/mariadb     latest       6b01262bc780   3 weeks ago     416 MB
	registry.do180.lab:5000/httpd       2.4-alpine   5a4f32436238   4 weeks ago     57.1 MB
	registry.do180.lab:5000/httpd       latest       c8ca530172a8   5 weeks ago     142 MB
	registry.redhat.io/rhel8/mysql-80   latest       7d3ef14d6a15   7 weeks ago     611 MB
	docker.io/library/centos            8            300e315adb2f   9 months ago    217 MB
	docker.io/library/centos            7            8652b9f0cb4c   10 months ago   212 MB
	[root@workstation s2-movie-info-service]#
	[root@workstation s2-movie-info-service]# podman build -f Dockerfile_t -t s2-movie-info-service .
	Downloaded from central: https://repo.maven.apache.org/maven2/commons-io/commons-io/2.5/commons-io-2.5.jar (209 kB at 66 kB/s)
	[INFO]
	[INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ s2-movie-info-service ---
	Downloading from central: https://repo.maven.apache.org/maven2/org/apache/maven/maven-core/3.0/maven-core-3.0.pom
	Downloaded from central: https://repo.maven.apache.org/maven2/org/codehaus/plexus/plexus-compiler-javac/2.8.4/plexus-compiler-javac-2.8.4.jar (21 kB at 13 kB/s)
	[INFO] Changes detected - recompiling the module!
	[INFO] Compiling 3 source files to /home/app/target/classes
	[INFO]
	[INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ s2-movie-info-service ---
	[INFO] Using 'UTF-8' encoding to copy filtered resources.
	[INFO] skip non existing resourceDirectory /home/app/src/test/resources
	[INFO]
	[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ s2-movie-info-service ---
	[INFO] Changes detected - recompiling the module!
	[INFO] Compiling 1 source file to /home/app/target/test-classes
	[INFO]
	[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ s2-movie-info-service ---
	Downloading from central: https://repo.maven.apache.org/maven2/org/apache/maven/surefire/maven-surefire-common/2.22.2/maven-surefire-common-2.22.2.pom
	Configuration$$EnhancerBySpringCGLIB$$50a218a2] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)

	  .   ____          _            __ _ _
	 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
	( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
	 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
	  '  |____| .__|_| |_|_| |_\__, | / / / /
	 =========|_|==============|___/=/_/_/_/
	 :: Spring Boot ::        (v2.1.5.RELEASE)
	
	2021-09-25 13:25:14.927  INFO 72 --- [           main] c.t.s.S2MovieInfoServiceApplicationTests : No active profile set, falling back to default profiles: default
	[INFO]
	[INFO] Results:
	[INFO]
	[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
	[INFO]
	[INFO]
	[INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ s2-movie-info-service ---
	[INFO] Building jar: /home/app/target/s2-movie-info-service-0.0.1-SNAPSHOT.jar
	[INFO]
	[INFO] --- spring-boot-maven-plugin:2.1.5.RELEASE:repackage (repackage) @ s2-movie-info-service ---
	Downloaded from central: https://repo.maven.apache.org/maven2/com/google/guava/guava/19.0/guava-19.0.jar (2.3 MB at 316 kB/s)
	[INFO] Replacing main artifact with repackaged archive
	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESS
	[INFO] ------------------------------------------------------------------------
	[INFO] Total time: 13:51 min
	[INFO] Finished at: 2021-09-25T13:27:27Z
	[INFO] ------------------------------------------------------------------------
	80a08e4d026b2f4fd317d15b81ae366ba6db72e857b43149c726e5679e144b75
	STEP 6: FROM openjdk:8
	Getting image source signatures
	Copying blob a83d4d9f4171 done
	Copying blob 2756ef5f69a5 done
	Copying blob 911ea9f2bd51 done
	Copying blob 785dffb36c6c done
	Copying blob 27b0a22ee906 done
	Copying blob 955615a668ce done
	Copying blob 88ab6c8cf10d done
	Copying config 08121337b7 done
	Writing manifest to image destination
	Storing signatures
	STEP 7: COPY --from=build /home/app/target/s2-movie-info-service-0.0.1-SNAPSHOT.jar s2-movie-info-service.jar
	ac4ee44d2aa4d3ae076c2c4245a7c653ae18b38c3d6e4c490d0ec011d4ff5a39
	STEP 8: EXPOSE 8082
	fbcdd935ad28d0f1dd4ed85d72a2cbae8ac2fc76e95547df355886aa05e6d4a9
	STEP 9: ENTRYPOINT ["java", "-jar", "s2-movie-info-service.jar"]
	STEP 10: COMMIT s2-movie-info-service
	f55dd4667d4041bc9d4682afa7fb1ecf14c3d912ece6e71c3538e837e4e0bd40
	f55dd4667d4041bc9d4682afa7fb1ecf14c3d912ece6e71c3538e837e4e0bd40
	[root@workstation s2-movie-info-service]# 
	[root@workstation s2-movie-info-service]# podman images
	REPOSITORY                          TAG                IMAGE ID       CREATED         SIZE
	localhost/s2-movie-info-service     latest             f55dd4667d40   4 minutes ago   578 MB
	<none>                              <none>             80a08e4d026b   8 minutes ago   238 MB
	localhost/httpdku                   latest             255d240a96a2   2 days ago      142 MB
	docker.io/voidarise1117/httpdku     v1                 255d240a96a2   2 days ago      142 MB
	docker.io/library/httpd             latest             5ebe6e00baf9   3 days ago      142 MB
	localhost/skeeter                   1.0                40917d4414a2   12 days ago     391 MB
	quay.io/feby_ichsan/duff-nginx      1.0                a4a391cc3e1d   12 days ago     569 MB
	registry:5000/httpd                 twerks             4e857675bd6d   12 days ago     57.1 MB
	docker.io/library/openjdk           8                  08121337b7a4   3 weeks ago     538 MB
	registry.do180.lab:5000/mariadb     latest             6b01262bc780   3 weeks ago     416 MB
	registry.do180.lab:5000/httpd       2.4-alpine         5a4f32436238   4 weeks ago     57.1 MB
	registry.do180.lab:5000/httpd       latest             c8ca530172a8   5 weeks ago     142 MB
	registry.redhat.io/rhel8/mysql-80   latest             7d3ef14d6a15   7 weeks ago     611 MB
	docker.io/library/centos            8                  300e315adb2f   9 months ago    217 MB
	docker.io/library/centos            7                  8652b9f0cb4c   10 months ago   212 MB
	docker.io/library/maven             3.5-jdk-8-alpine   fb4bb0d89941   2 years ago     122 MB
	[root@workstation s2-movie-info-service]#
	

* [**build image and run as container-1**]
