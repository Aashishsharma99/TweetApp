import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.*;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.TextMessage;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.annotation.Lazy;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import com.verizon.onemsg.omppservice.receiver.BatchDisconnectIBMQReceiver;
import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import com.vzw.cc.util.Encoder;

@SpringBootTest
@ExtendWith(SpringExtension.class)
class BatchDisconnectIBMQReceiverTest {

    @Autowired
    private BatchDisconnectIBMQReceiver receiver;

    @Mock
    private MDNCleanupService mdnCleanupServiceMock;

    @Test
    void testOnMessageWithValidXml() throws JMSException {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(textMessageMock.getText()).thenReturn("<xml>your_xml_here</xml>");

        // Act
        receiver.onMessage(textMessageMock);

        // Assert
        verify(mdnCleanupServiceMock).doDisconnectMDNProcessing("<xml>your_xml_here</xml>");
    }

    @Test
    void testOnMessageWithNullXml() throws JMSException {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(textMessageMock.getText()).thenReturn(null);

        // Act
        receiver.onMessage(textMessageMock);

        // Assert
        verifyNoInteractions(mdnCleanupServiceMock);
    }

    @Test
    void testOnMessageWithNonTextMessage() throws JMSException {
        // Arrange
        Message messageMock = mock(Message.class);
        when(messageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);

        // Act
        receiver.onMessage(messageMock);

        // Assert
        verifyNoInteractions(mdnCleanupServiceMock);
    }

    @Test
    void testOnMessageWithException() throws JMSException {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(textMessageMock.getText()).thenThrow(new JMSException("Simulated JMSException"));

        // Act
        assertDoesNotThrow(() -> receiver.onMessage(textMessageMock));

        // Assert
        verifyNoInteractions(mdnCleanupServiceMock);
    }
}
