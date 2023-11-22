import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.*;
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
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.annotation.Lazy;
import org.w3c.dom.Document;

import com.verizon.onemsg.omppservice.receiver.MDNActivateIBMReceiver;
import com.verizon.onemsg.omppservice.service.ActivateProcessor;
import com.verizon.onemsg.omppservice.util.XmlUtil;

@SpringBootTest
@ExtendWith(MockitoExtension.class)
class MDNActivateIBMReceiverTest {

    @Autowired
    private MDNActivateIBMReceiver receiver;

    @Mock
    private ActivateProcessor activateProcessorMock;

    @Test
    void testOnMessage() throws JMSException {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getText()).thenReturn("<xml>your_xml_here</xml>");

        // Act
        assertDoesNotThrow(() -> receiver.onMessage(textMessageMock));

        // Assert
        verify(activateProcessorMock).processActivate(any(Document.class), eq(AppConstants.Processes.ACTIVATION));
        verifyNoMoreInteractions(activateProcessorMock);
    }

    @Test
    void testOnMessageWithException() throws JMSException {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getText()).thenReturn("<xml>your_xml_here</xml>");
        doThrow(new RuntimeException("Simulated exception")).when(activateProcessorMock)
                .processActivate(any(Document.class), eq(AppConstants.Processes.ACTIVATION));

        // Act
        assertDoesNotThrow(() -> receiver.onMessage(textMessageMock));

        // Assert
        verify(activateProcessorMock).processActivate(any(Document.class), eq(AppConstants.Processes.ACTIVATION));
        verifyNoMoreInteractions(activateProcessorMock);
    }
}
