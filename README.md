# Azure-Service-Bus-Event-Hubs

Azure Service Bus and Event Hubs are two distinct Azure messaging services designed for different use cases. Here's a comprehensive comparison, use cases, and examples for both.

---

## **Azure Service Bus**
Azure Service Bus is a fully managed enterprise messaging service designed for message-based integration. It supports reliable messaging between services and applications, decoupling the components.

### **Key Features**
- **Message Queues**: Asynchronous message delivery using queues.
- **Topics and Subscriptions**: Publish/subscribe model for fan-out messaging.
- **Dead Letter Queue (DLQ)**: Handles messages that can't be processed.
- **Transactions**: Supports atomic operations for sending and receiving messages.

### **Use Cases**
- Decoupling microservices.
- Order processing and inventory updates.
- Workflow orchestration in business applications.

---

### **Service Bus Queue Example**
#### **Send Message to Queue (Python)**
```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

connection_string = "<your_service_bus_connection_string>"
queue_name = "<your_queue_name>"

servicebus_client = ServiceBusClient.from_connection_string(connection_string)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=queue_name)
    with sender:
        message = ServiceBusMessage("Hello, Service Bus!")
        sender.send_messages(message)
        print("Message sent to queue.")
```

#### **Receive Message from Queue (Python)**
```python
from azure.servicebus import ServiceBusClient

connection_string = "<your_service_bus_connection_string>"
queue_name = "<your_queue_name>"

servicebus_client = ServiceBusClient.from_connection_string(connection_string)

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=queue_name, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received:", msg)
            receiver.complete_message(msg)
```

---

### **Service Bus Topics and Subscriptions Example**
#### **Send Message to Topic**
```python
sender = servicebus_client.get_topic_sender(topic_name="<your_topic_name>")
with sender:
    message = ServiceBusMessage("Message for topic")
    sender.send_messages(message)
```

#### **Receive Message from Subscription**
```python
receiver = servicebus_client.get_subscription_receiver(
    topic_name="<your_topic_name>", 
    subscription_name="<your_subscription_name>", 
    max_wait_time=5
)
with receiver:
    for msg in receiver:
        print("Received from subscription:", msg)
        receiver.complete_message(msg)
```

---

## **Azure Event Hubs**
Azure Event Hubs is a big data streaming platform and event ingestion service. It is designed for high-throughput data streaming and event-driven architectures.

### **Key Features**
- **Partitioned Event Streams**: Ensures ordered processing within a partition.
- **Capture**: Streams data directly to Azure Blob Storage or Data Lake.
- **Consumer Groups**: Supports multiple parallel consumers for scalability.
- **Replayable Data**: Retains events for a configurable time (up to 90 days).

### **Use Cases**
- Real-time telemetry and log ingestion.
- Streaming analytics and data pipelines.
- Internet of Things (IoT) data collection.

---

### **Event Hubs Example**
#### **Send Events to Event Hub (Python)**
```python
from azure.eventhub import EventHubProducerClient, EventData

connection_string = "<your_event_hub_connection_string>"
eventhub_name = "<your_event_hub_name>"

producer = EventHubProducerClient.from_connection_string(connection_string, eventhub_name=eventhub_name)

with producer:
    event_data_batch = producer.create_batch()
    event_data_batch.add(EventData("First event"))
    event_data_batch.add(EventData("Second event"))
    producer.send_batch(event_data_batch)
    print("Events sent.")
```

#### **Receive Events from Event Hub (Python)**
```python
from azure.eventhub import EventHubConsumerClient

connection_string = "<your_event_hub_connection_string>"
eventhub_name = "<your_event_hub_name>"
consumer_group = "$Default"

def on_event(partition_context, event):
    print(f"Received event: {event.body_as_str()}")
    partition_context.update_checkpoint(event)

consumer = EventHubConsumerClient.from_connection_string(
    connection_string, consumer_group, eventhub_name
)

with consumer:
    consumer.receive(
        on_event=on_event,
        starting_position="-1"  # Start reading from the beginning
    )
```

---

## **Comparison: Azure Service Bus vs Event Hubs**

| Feature               | Azure Service Bus                               | Azure Event Hubs                             |
|-----------------------|-------------------------------------------------|---------------------------------------------|
| **Purpose**           | Message queueing and reliable messaging         | Event streaming and telemetry ingestion      |
| **Message Size**      | Up to 256 KB                                    | Up to 1 MB                                   |
| **Retention**         | Until processed or DLQ                          | Configurable, up to 90 days                 |
| **Protocol**          | AMQP, HTTP                                      | AMQP, Kafka                                 |
| **Message Ordering**  | Yes (Queues/Partitions)                         | Yes (Within Partitions)                     |
| **Consumer Model**    | Competing consumers                             | Parallel processing with Consumer Groups    |
| **Use Case Examples** | Order processing, command handling              | IoT telemetry, log streaming                |

---

## **Integration Example**
Azure Service Bus and Event Hubs can be integrated to combine messaging and streaming use cases. For example, use Event Hubs to ingest telemetry data and Service Bus to send processed notifications to downstream systems.

Need more detailed help or specific scenarios? Let me know!
