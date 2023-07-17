# springboot-elastic-apm

## Start the predev environment [terminal 1]:

```mvn spring-boot:run -Dspring-boot.run.profiles=predev```

Since the predev environment has the `elastic.apm.enabled` property set to false and the ElasticApmConfig is annotated with `@ConditionalOnProperty(value = “elastic.apm.enabled”, havingValue = “true”)` , this `@Config` class is not injected into the Spring’s context, and the Elastic APM agent would not get attached. Notice that we didn’t even run the Elastic APM server yet.

## Start the Elasticsearch, Kibana, and Elastic APM servers [terminal 2]:

```docker-compose up```

Once all the images are downloaded, and containers have started, navigate in a browser to `http://localhost:5601` to access Kibana.

## Start the dev environment [terminal 3]:

```mvn spring-boot:run -Dspring-boot.run.profiles=dev```

Notice the verbosity of the logs on this environment since we’ve set the Elastic’s agent log level to DEBUG .

## Start the staging environment [terminal 4]:

```mvn spring-boot:run -Dspring-boot.run.profiles=staging```

In this environment, the log level is set to INFO and it’s noticeably less verbose.

## Start the prod environment [terminal 5]:

```mvn spring-boot:run -Dspring-boot.run.profiles=prod```

By this stage, if you head to the APM dashboard on Kibana, you should find a service called elastic-apm-spring-boot-integration with three environments, dev, staging, and prod as shown in the figure below.

## Generate some traffic on the different environments using the test-apis.sh script.

The script will call each endpoint defined in the TestController (/super-fast, /fast, /slow, and /super-slow ) ten times in each environment. Notice that the prod environment does not expose the /super-slow endpoint. This is meant to simulate a scenario where a new feature is being tested and is not yet deployed to production.

```
chmod +x test-apis.sh
./test-apis.sh
```

Wait for the script to log Iteration 10 , then wait for about another minutes until the `super-slow` APIs return.

Head again to the APM dashboard on Kibana and click on the `elastic-apm-spring-boot-integration` service to get more details:

