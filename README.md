import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNull;
import static org.junit.jupiter.api.Assertions.assertThrows;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.TextMessage;

import org.apache.logging.log4j.Logger;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.Lazy;

import com.verizon.onemsg.omppservice.receiver.BatchDisconnectIBMQReceiver;
import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import com.vzw.cc.util.Encoder;

class BatchDisconnectIBMQReceiverTest {

    private BatchDisconnectIBMQReceiver receiver;
    private MDNCleanupService mdnCleanupServiceMock;
    private TestLogger testLogger;

    @BeforeEach
    void setUp() {
        mdnCleanupServiceMock = new TestMDNCleanupService();
        receiver = new BatchDisconnectIBMQReceiver(() -> mdnCleanupServiceMock);
        testLogger = new TestLogger();
        receiver.log = testLogger;
    }

    @Test
    void testOnMessageWithValidXml() throws JMSException {
        // Arrange
        TestTextMessage textMessage = new TestTextMessage("<xml>your_xml_here</xml>");

        // Act
        receiver.onMessage(textMessage);

        // Assert
        assertEquals("deliveryCount : 1", testLogger.getLastLog());
        assertEquals("Request XML : <xml>your_xml_here</xml>", testLogger.getLastLog());
        assertEquals("End BatchMDNDisconnectMDB", testLogger.getLastLog());
        assertEquals("<xml>your_xml_here</xml>", ((TestMDNCleanupService) mdnCleanupServiceMock).getLastProcessedXml());
    }

    @Test
    void testOnMessageWithNullXml() throws JMSException {
        // Arrange
        TestTextMessage textMessage = new TestTextMessage(null);

        // Act
        receiver.onMessage(textMessage);

        // Assert
        assertEquals("deliveryCount : 1", testLogger.getLastLog());
        assertNull(testLogger.getLastLog());
        assertEquals("finally block", testLogger.getLastLog());
        assertNull(((TestMDNCleanupService) mdnCleanupServiceMock).getLastProcessedXml());
    }

    @Test
    void testOnMessageWithNonTextMessage() throws JMSException {
        // Arrange
        TestMessage message = new TestMessage();

        // Act
        receiver.onMessage(message);

        // Assert
        assertEquals("deliveryCount : 1", testLogger.getLastLog());
        assertEquals("finally block", testLogger.getLastLog());
        assertNull(((TestMDNCleanupService) mdnCleanupServiceMock).getLastProcessedXml());
    }

    @Test
    void testOnMessageWithException() throws JMSException {
        // Arrange
        TestTextMessage textMessage = new TestTextMessageWithException();

        // Act
        assertThrows(Exception.class, () -> receiver.onMessage(textMessage));

        // Assert
        assertEquals("deliveryCount : 1", testLogger.getLastLog());
        assertNull(testLogger.getLastLog());
        assertEquals("finally block", testLogger.getLastLog());
        assertNull(((TestMDNCleanupService) mdnCleanupServiceMock).getLastProcessedXml());
    }

    // Helper classes for testing
    private static class TestTextMessage implements TextMessage {
        private final String text;

        public TestTextMessage(String text) {
            this.text = text;
        }

        @Override
        public int getIntProperty(String name) throws JMSException {
            return 1;
        }

        @Override
        public String getText() throws JMSException {
            return text;
        }

        // Implement other methods as needed
    }

    private static class TestMessage implements Message {
        @Override
        public int getIntProperty(String name) throws JMSException {
            return 1;
        }

        // Implement other methods as needed
    }

    private static class TestTextMessageWithException extends TestTextMessage {
        public TestTextMessageWithException() {
            super(null);
        }

        @Override
        public String getText() throws JMSException {
            throw new JMSException("Simulated JMSException");
        }
    }

    private static class TestLogger implements Logger {
        private String lastLog;

        public String getLastLog() {
            return lastLog;
        }

        @Override
        public void debug(CharSequence charSequence) {
            lastLog = charSequence.toString();
        }

        // Implement other methods as needed
    }

    private static class TestMDNCleanupService implements MDNCleanupService {
        private String lastProcessedXml;

        public String getLastProcessedXml() {
            return lastProcessedXml;
        }

        @Override
        public void doDisconnectMDNProcessing(String xmlMsg) {
            lastProcessedXml = xmlMsg;
        }

        // Implement other methods as needed
    }
}
