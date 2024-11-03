# demo-resilience4j

This a learning demo for resilience4j  + spring-boot-3 


# Resilience patterns

Problem

   [WIP] Diagram fallback
   
   * [WIP]  Resilience configuration
   * [WIP ] grafana dashboard

 We need to add a mechanism to detect the problem in microservices that receive errors for the other microservices. 

payment-service : 
- Has hight error rate, so   we can create a generic fallback method for all exceptions and specific erros on create order 
   
inventory-service:
- Has high comsumtion ...   

## Designing for gracefull degradation

To prevent or reduce the number of requests that your microservices send to APIs that are unhealthy or have a high rate of errors. 
Also, you will add a fallback method to have a solution when an exception happens in some part of the flow.

There are two ways to add a circuit breaker: in the connectors, or in the services. You can add it at the layer of services when it is unnecessary to execute all the logic, because you have a fallback method that gives a default response so the services don’t do something irrelevant.

You can add a circuit breaker on the connectors when the logic of the services is too complex to create a fallback method and the services do a lot of things other than just get the information for the connectors (e.g., they persist some information in the cache).

In this particular case, in payment-service and inventory-service produce the errors (latency or exceptions), so we need to add resilence4j in checkout-service, adding the annotation of circuit breaker in the checkout-services: getProductDetails and createOrder methods

- checkout createOrder: We create a fallbackmethod that receives a httpException and create a fallback for all exceptions
- fallback method: In checkout getProductDetails we create a fallbackmethod that receives a RuntimeException and create a response with last cached results


# Rate limiter

 Mechanism to limit the number of requests which you know will not support a large number of requests in a period.
this mechanism helps to reduce the number of requests to certain microservices which have a huge number of requests. 

If a service receives a huge number of requests in a period, it could produce a DDoS (Distributed Denial of Service) attack affecting all of the microservices. This approach of limiting the number of requests preserves all of the infrastructure. 
Also, you will use the fallback method to return a particular exception in the case that the requests exceed the maximum number of requests.


There are two formats for limiting the number of requests:

**Consumer side:** In this type of rate limiter, the responsibility of limiting the number of requests is in each microservice that uses another microservice. This approach is a good option when you don’t have the chance to change the other microservice but you know more or less the limit of each instance of that microservice. 
 limit = (max number of requests / number of instances of your microservice)

**Provider side:** In this type of rate limiter the responsibility for limiting the number of requests is in the microservices that provide the information,

In our case inventory-service. This microservice will have a rate limiter to reduce the number of requests that it can support. This approach is an excellent alternative when you know the maximum number of requests that one instance of your microservice could support before becoming unhealthy.
 
