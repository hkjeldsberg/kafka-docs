# Testing in Kafka

## Testing Producers and Consumers

The Kafka Producer and Consumer APIs provide the `MockProducer` and `MockConsumer` classes, respectively. For instance,
creating a unit test of a producer can be performed as follows:

```java
import org.apache.kafka.clients.producer.MockProducer;

public class MyProducerTest {
    
    MockProducer<Integer, String> mockProducer;
    MyProducer myProducer;
    
    @Before
    public void setUp() {
        mockProducer = new MockProducer<>(false, new IntegerSerializer(), new StringSerializer());
        myProducer = new MyProducer();
        myProducer.producer = mockProducer;
    }
    
    @Test
    public void testPublishRecord_sent_data() {
        // Perform a simple test to verify that the producer sends the correct data to the correct topic when publishRecord is called.
        myProducer.publishRecord(1, "Test Data");
        
        mockProducer.completeNext();
        
        // Do some asserts   
    }
}
```

## Testing Streams

Kafka provides a separate library for testing streams: `kafka-streams-test-utils` contining test fixtures for Kafka
Streams applicaitons. This includes

- **TopologyTestDriver**: For feeding records, simulating the topology, and returning (altered) records
- **ConsumerRecordFactory**: Converts consumer record data into byte arrays
- **OutputVerifier**: Provides helper methods for verification of records