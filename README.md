import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.TextMessage;

import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import com.verizon.onemsg.omppservice.service.MDNCleanupService;

@ExtendWith(MockitoExtension.class)
class BatchDisconnectIBMQReceiverTest {

    @InjectMocks
    private BatchDisconnectIBMQReceiver receiver;

    @Mock
    private MDNCleanupService mdnCleanupService;

    @Mock
    private Message message;

    @Test
    void testOnMessage() throws JMSException {
        // Arrange
        when(message.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(message instanceof TextMessage).thenReturn(true);

        TextMessage textMessage = mock(TextMessage.class);
        when((TextMessage) message).thenReturn(textMessage);

        String xmlMsg = "<xml>your_xml_here</xml>";
        when(textMessage.getText()).thenReturn(xmlMsg);

        // Act
        receiver.onMessage(message);

        // Assert
        verify(mdnCleanupService, times(1)).doDisconnectMDNProcessing(xmlMsg);
    }

    @Test
    void testOnMessageWithNullXml() throws JMSException {
        // Arrange
        when(message.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(message instanceof TextMessage).thenReturn(true);

        TextMessage textMessage = mock(TextMessage.class);
        when((TextMessage) message).thenReturn(textMessage);

        when(textMessage.getText()).thenReturn(null);

        // Act
        receiver.onMessage(message);

        // Assert
        verify(mdnCleanupService, never()).doDisconnectMDNProcessing(any());
    }
}
