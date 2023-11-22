import static org.mockito.Mockito.*;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.TextMessage;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import com.verizon.onemsg.omppservice.service.MDNCleanupService;

class BatchDisconnectIBMQReceiverTest {

    private BatchDisconnectIBMQReceiver receiver;
    private MDNCleanupService mdnCleanupService;

    @BeforeEach
    void setUp() {
        mdnCleanupService = mock(MDNCleanupService.class);
        receiver = new BatchDisconnectIBMQReceiver(mdnCleanupService);
    }

    @Test
    void testOnMessage() throws JMSException {
        // Arrange
        Message message = mock(Message.class);
        TextMessage textMessage = mock(TextMessage.class);

        when(message.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(message instanceof TextMessage).thenReturn(true);
        when((TextMessage) message).thenReturn(textMessage);
        when(textMessage.getText()).thenReturn("<xml>your_xml_here</xml>");

        // Act
        receiver.onMessage(message);

        // Assert
        verify(mdnCleanupService, times(1)).doDisconnectMDNProcessing("<xml>your_xml_here</xml>");
    }

    @Test
    void testOnMessageWithException() throws JMSException {
        // Arrange
        Message message = mock(Message.class);

        when(message.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(message instanceof TextMessage).thenReturn(true);
        when(((TextMessage) message).getText()).thenThrow(new JMSException("Simulated exception"));

        // Act
        receiver.onMessage(message);

        // Assert (you can customize the assertions based on your specific needs)
        // For example, you can verify that the logger is called with the expected arguments.
    }
}
