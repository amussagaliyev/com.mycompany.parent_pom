###This is a parent project to manage all project in consistent way.

###To clone project:

```
git clone https://github.com/amussagaliyev/com.mycompany.parent_pom.git
git checkout release/1.0
git submodule init
git submodule update
```

You will get release/1.0 branch.

###To build whole project
(including docker images build) please make sure that you have the following stuff

1. maven
2. docker
3. docker hub server configured within maven's settings.xml:

```
<server>
  <id>docker.io</id>
  <username>USER_NAME</username>
  <password>PASSWORD</password>
  <configuration>
      <email>email@email.com</email>
  </configuration>
</server>
```

and execute following command from parent project dir

```
mvn clean deploy
```

Make sure you have following images built

```
a142857/mycompany.product_review_webapp:1.0-SNAPSHOT
a142857/mycompany.product_review_processor_webapp:1.0-SNAPSHOT
a142857/mycompany.notifier_webapp:1.0-SNAPSHOT
a142857/mycompany.db:1.0-SNAPSHOT
a142857/mycompany.tomcat:1.0-SNAPSHOT
a142857/mycompany.redis:1.0-SNAPSHOT
```

To run

1. Run PostgreSQL server
```
docker run -d -p 5432:5432 --name mycompany.db mycompany.db
```

2. Run Redis server
```
docker run -p 6379:6379 --name mycompany.redis mycompany.redis
```

3. Run RESTful service
```
docker run -p 8080:8080 --link mycompany.db:mycompany.db --link mycompany.redis:mycompany.redis --name mycompany.product_review_webapp mycompany.product_review_webapp
```

4. Run Reviews Processor Service
```
docker run -p 8180:8180 --link mycompany.db:mycompany.db --link mycompany.redis:mycompany.redis --name mycompany.product_review_processor_webapp mycompany.product_review_processor_webapp
```

5. Run Notifier Service
```
docker run -p 8080:8080 --link mycompany.db:mycompany.db --link mycompany.redis:mycompany.redis --name mycompany.notifier_webapp mycompany.notifier_webapp
```
