import com.ibm.mq.jms.MQQueueConnectionFactory;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.jms.connection.JmsTransactionManager;
import org.springframework.jms.core.JmsTemplate;

import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest
class IBMMQConfigTest {

    @Autowired
    private MQQueueConnectionFactory mqQueueConnectionFactory;

    @Autowired
    private JmsTransactionManager jmsTransactionManager;

    @Autowired
    private JmsTemplate jmsTemplate;

    // Add more tests for other beans if needed

    @Test
    void testMqQueueConnectionFactory() {
        assertNotNull(mqQueueConnectionFactory);
        // Add assertions for specific properties if needed
    }

    @Test
    void testJmsTransactionManager() {
        assertNotNull(jmsTransactionManager);
        // Add assertions for specific properties if needed
    }

    @Test
    void testJmsTemplate() {
        assertNotNull(jmsTemplate);
        // Add assertions for specific properties if needed
    }
    
    // Add more tests for other beans if needed
}
