# Task-1
README
import akka.actor.ActorSystem //provides the actor system for handling concurrency.

import akka.http.scaladsl.Http //provides the HTTP server and client functionality.

import akka.http.scaladsl.model._ //This imports various models related to HTTP, such as requests and responses.

import akka.http.scaladsl.server.Directives._ //This imports directives that are used to define routes for the HTTP server.

import akka.stream.ActorMaterializer //This imports the ActorMaterializer, which is required for materializing streams.

import spray.json._ //This imports the Spray JSON library, which is used for JSON serialization and deserialization.

import org.apache.kafka.clients.producer.{KafkaProducer, ProducerRecord} //This imports classes needed to create a Kafka producer, which is used for sending messages to Kafka topics.

import com.rabbitmq.client.ConnectionFactory //This imports the ConnectionFactory class from RabbitMQ, which is used to create
connections to RabbitMQ servers.

case class ContainerRoutingRequest(containers: List[Int], routes: List[List[Int]]) //This defines a case class for the incoming request, which contains a list of container IDs and their routes.

case class ContainerRoutingResponse(optimalRoute: List[Int]) //This defines a case class for the response, which contains the optimal route as a list of integers.

object ContainerRoutingAPI { //This defines a singleton object that will hold the API functionality.

implicit val system = ActorSystem("container-routing-api") // This creates an implicit ActorSystem named "container-routing-api", which is used throughout the application.

implicit val materializer = ActorMaterializer() //This creates an implicit ActorMaterializer that is necessary for handling streams.

implicit val executionContext = system.dispatcher // This sets the execution context to the dispatcher of the ActorSystem, which is used for executing futures.

// Kafka producer val props = new java.util.Properties() //This creates a new Properties object to configure the Kafka producer.

props.put("bootstrap.servers", "localhost:9092") //This sets the address of the Kafka broker.

props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer") //This specifies the serializer for the keys of the messages sent to Kafka.

props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer") //This specifies the serializer for the values of the messages sent to Kafka.

val kafkaProducer = new KafkaProducerString, String //This creates a new Kafka producer with the specified properties.

// RabbitMQ connection

val factory = new ConnectionFactory() //This sets the RabbitMQ server host to localhost.

factory.setHost("localhost") //This sets the RabbitMQ server host to localhost.

val rabbitMQConnection = factory.newConnection() //This establishes a new connection to the RabbitMQ server.

val channel = rabbitMQConnection.createChannel() //This creates a new channel for communication with RabbitMQ.

val route = path("optimize") { // This defines a route for the API that listens for POST requests at the path "/optimize".
