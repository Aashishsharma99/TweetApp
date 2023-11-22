import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.*;
import static org.mockito.Mockito.*;

import javax.jms.TextMessage;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.context.annotation.Lazy;
import org.w3c.dom.Document;

import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.handler.FamilyBaseSFOHandler;
import com.verizon.onemsg.omppservice.handler.MDNChangeHandler;
import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.verizon.onemsg.omppservice.service.ChangeDataFeaturesProcessor;
import com.verizon.onemsg.omppservice.service.VisionAlertsProcessor;
import com.verizon.onemsg.omppservice.util.HttpCall;
import com.verizon.onemsg.omppservice.util.ReadWriteXml;

@SpringBootTest
@ExtendWith(MockitoExtension.class)
class MDNChangeIBMreceiverTest {

    @InjectMocks
    private MDNChangeIBMreceiver receiver;

    @Mock
    private JmsTemplate jmsTemplate;

    @Mock
    private MDNChangeHandler mdnChangeHandler;

    @Mock
    private FamilyBaseSFOHandler familySfoHandler;

    @Mock
    private ChangeDataFeaturesProcessor changeDataFeaturesProcessor;

    @Mock
    private ReadWriteXml readWriteXml;

    @Mock
    private HttpCall httpcall;

    @MockBean
    private AppProperties prop;

    @Test
    void testOnMessage() throws Exception {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(2);
        when(textMessageMock.getText()).thenReturn("<xml>your_xml_here</xml>");

        // Act
        assertDoesNotThrow(() -> receiver.onMessage(textMessageMock));

        // Assert
        verify(jmsTemplate).convertAndSend(any(), anyString());
        verifyNoMoreInteractions(jmsTemplate);
    }

    @Test
    void testOnMessageWithRetry() throws Exception {
        // Arrange
        TextMessage textMessageMock = mock(TextMessage.class);
        when(textMessageMock.getIntProperty("JMSXDeliveryCount")).thenReturn(4);
        when(textMessageMock.getText()).thenReturn("<xml>your_xml_here</xml>");

        // Act
        assertDoesNotThrow(() -> receiver.onMessage(textMessageMock));

        // Assert
        verify(jmsTemplate, never()).convertAndSend(any(), anyString());
        verifyNoMoreInteractions(jmsTemplate);
    }

    // Additional test cases can be added for different scenarios
}
