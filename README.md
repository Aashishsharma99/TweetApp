import static org.mockito.Mockito.*;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.TextMessage;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.context.annotation.Lazy;

import com.verizon.onemsg.omppservice.receiver.BatchDisconnectIBMQReceiver;
import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import com.vzw.cc.util.Encoder;

class BatchDisconnectIBMQReceiverTest {

    @Mock
    private Logger logMock;

    @Mock
    private MDNCleanupService mdnCleanupServiceMock;

    @InjectMocks
    private BatchDisconnectIBMQReceiver receiver;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    void testOnMessage() throws JMSException {
        // Arrange
        Message messageMock = mock(Message.class);
        TextMessage textMessageMock = mock(TextMessage.class);

        when(messageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(messageMock instanceof TextMessage).thenReturn(true);
        when((TextMessage) messageMock).thenReturn(textMessageMock);

        String originalXmlMsg = "<xml>your_xml_here</xml>";
        String encodedXmlMsg = Encoder.makeXmlValid(originalXmlMsg);

        when(textMessageMock.getText()).thenReturn(originalXmlMsg);

        // Act
        receiver.onMessage(messageMock);

        // Assert
        verify(logMock).debug("deliveryCount : 1");
        verify(logMock).debug("Request XML : " + originalXmlMsg);
        verify(mdnCleanupServiceMock).doDisconnectMDNProcessing(encodedXmlMsg);
        verify(logMock).debug("End BatchMDNDisconnectMDB");
        verifyNoMoreInteractions(logMock, mdnCleanupServiceMock);
    }

    @Test
    void testOnMessageWithNullXml() throws JMSException {
        // Arrange
        Message messageMock = mock(Message.class);
        TextMessage textMessageMock = mock(TextMessage.class);

        when(messageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(messageMock instanceof TextMessage).thenReturn(true);
        when((TextMessage) messageMock).thenReturn(textMessageMock);

        when(textMessageMock.getText()).thenReturn(null);

        // Act
        receiver.onMessage(messageMock);

        // Assert
        verify(logMock).debug("deliveryCount : 1");
        verify(mdnCleanupServiceMock, never()).doDisconnectMDNProcessing(any());
        verify(logMock, never()).debug("Request XML : ");
        verify(logMock).debug("finally block");
        verifyNoMoreInteractions(logMock, mdnCleanupServiceMock);
    }

    @Test
    void testOnMessageWithNonTextMessage() throws JMSException {
        // Arrange
        Message messageMock = mock(Message.class);

        when(messageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(1);
        when(messageMock instanceof TextMessage).thenReturn(false);

        // Act
        receiver.onMessage(messageMock);

        // Assert
        verify(logMock).debug("deliveryCount : 1");
        verify(logMock).debug("finally block");
        verify(mdnCleanupServiceMock, never()).doDisconnectMDNProcessing(any());
        verify(logMock, never()).debug("Request XML : ");
        verifyNoMoreInteractions(logMock, mdnCleanupServiceMock);
    }

    // Add more test cases as needed
}
