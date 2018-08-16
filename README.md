### This is a parent project to join and manage following projects

1. https://github.com/amussagaliyev/com.mycompany.api.api_product_review
2. https://github.com/amussagaliyev/com.mycompany.application.notifier_webapp
3. https://github.com/amussagaliyev/com.mycompany.application.product_review_processor_webapp
4. https://github.com/amussagaliyev/com.mycompany.application.product_review_webapp
5. https://github.com/amussagaliyev/com.mycompany.bdk.bdk_product_review
6. https://github.com/amussagaliyev/com.mycompany.bdk.bdk_product_review_queue
7. https://github.com/amussagaliyev/com.mycompany.config.webapp_config
8. https://github.com/amussagaliyev/com.mycompany.docker.db
9. https://github.com/amussagaliyev/com.mycompany.docker.redis
10. https://github.com/amussagaliyev/com.mycompany.docker.tomcat
11. https://github.com/amussagaliyev/com.mycompany.sdk.model_core
12. https://github.com/amussagaliyev/com.mycompany.sdk.model_product
13. https://github.com/amussagaliyev/com.mycompany.sdk.model_product_review
14. https://github.com/amussagaliyev/com.mycompany.sdk.sdk_redis

`com.mycompany.application` - web appplication

`com.mycompany.bdk` - Service layer

`com.mycompany.sdk` - mostly JPA entities and DAOs

`com.mycompany.docker` - docker images based infrastructure

### To clone project:

```
git clone https://github.com/amussagaliyev/com.mycompany.parent_pom.git
git checkout release/1.0
git submodule init
git submodule update
```

You will get release/1.0 branch.

### To build whole project
including docker images build please make sure that you have the following stuff

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

### To run and check

1. Run PostgreSQL server
```
docker run -d -p 5432:5432 --name mycompany.db a142857/mycompany.db:1.0-SNAPSHOT
```

2. Run Redis server
```
docker run -p 6379:6379 --name mycompany.redis a142857/mycompany.redis:1.0-SNAPSHOT
```

3. Run RESTful service
```
docker run -p 8080:8080 --link mycompany.db:mycompany.db --link mycompany.redis:mycompany.redis --name mycompany.product_review_webapp a142857/mycompany.product_review_webapp:1.0-SNAPSHOT
```

4. Run Reviews Processor Service
```
docker run --link mycompany.db:mycompany.db --link mycompany.redis:mycompany.redis --name mycompany.product_review_processor_webapp a142857/mycompany.product_review_processor_webapp:1.0-SNAPSHOT
```

5. Run Notifier Service
```
docker run -p --link mycompany.db:mycompany.db --link mycompany.redis:mycompany.redis --name mycompany.notifier_webapp a142857/mycompany.notifier_webapp:1.0-SNAPSHOT
```

6. Try to POST request to http://localhost:8080/product_review/api/reviews with the following body
```
{
"name": "Elvis Presley",
"email": "theking@elvismansion.com",
"productid": "4",
"review": "I really fee love the product and will recommend!",
"rating": 4
}
```

and headers

```
Content-Type: application/json
Authorization: Basic dXNlcjE6cGFzc3dvcmQ=
```

Authorization header's value represents "user1/password" credentials. This is the only predefined user within system.

Response should be something like:
```
{
"success": true,
"reviewId": 5
}
```

and on the "Notifier Service" console (see step 5) message starting with "Dear user..." should appear

All images available on docker.io

Project deployed on AWS EC2 instance and can be called using the following URL (can be out of sync):

http://34.219.134.146:8080/product_review/api/reviews
