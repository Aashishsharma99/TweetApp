import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.service.ActivateProcessor;
import com.verizon.onemsg.omppservice.util.XmlUtil;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.w3c.dom.Document;

import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class MDNActivateReceiverTest {

    @Mock
    private ActivateProcessor processor;

    @Mock
    private XmlUtil xmlUtil;

    @InjectMocks
    private MDNActivateReceiver mdnActivateReceiver;

    @Test
    void onMessage_shouldProcessActivateAndLogInfo() {
        // Arrange
        String message = "yourTestMessage";
        Document doc = mock(Document.class);
        when(xmlUtil.loadXMLFromString(message)).thenReturn(doc);
        when(processor.processActivate(doc, AppConstants.Processes.ACTIVATION)).thenReturn("success");

        // Act
        mdnActivateReceiver.onMessage(message);

        // Assert
        verify(processor).processActivate(doc, AppConstants.Processes.ACTIVATION);
        // Add more assertions as needed
    }

    @Test
    void onMessage_shouldLogErrorIfExceptionThrown() {
        // Arrange
        String message = "yourTestMessage";
        when(xmlUtil.loadXMLFromString(message)).thenThrow(new RuntimeException("Simulated exception"));

        // Act
        mdnActivateReceiver.onMessage(message);

        // Assert
        // Verify that log.error is called with the expected parameters
        // For example, you can use ArgumentMatchers.eq for matching specific arguments
        verify(log).error(eq("Vision Activities"), eq("CustActivityActivateMDB"), eq("onMessage"),
                anyString(), eq("Error"), any(Throwable.class));
    }

    // Add more test cases as needed
}
