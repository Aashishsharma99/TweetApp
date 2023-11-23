import static org.junit.Assert.assertEquals;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.w3c.dom.Document;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.verizon.onemsg.omppservice.service.CommonProcessor;

public class FamilyBaseSFOHandlerTest {

    @Mock
    private AppProperties appProperties;

    @Mock
    private CommonProcessor processor;

    @InjectMocks
    private FamilyBaseSFOHandler handler;

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testProcessWithMatchingSFOIDAndEffectiveDate() throws Exception {
        // Arrange
        Document document = mock(Document.class);
        NodeList sfoIdNodes = mock(NodeList.class);
        Node sfoIdNode = mock(Node.class);
        NodeList childNodes = mock(NodeList.class);

        when(document.evaluate("//EDR_CPF//CHANGE_DATA_FEATURES//SFO_ID", document, any())).thenReturn(sfoIdNodes);
        when(sfoIdNodes.getLength()).thenReturn(1);
        when(sfoIdNodes.item(0)).thenReturn(sfoIdNode);

        when(sfoIdNode.hasChildNodes()).thenReturn(true);
        when(sfoIdNode.getChildNodes()).thenReturn(childNodes);
        when(childNodes.getLength()).thenReturn(1);
        when(childNodes.item(0).getNodeName()).thenReturn("SFO_ID");
        when(childNodes.item(0).getTextContent()).thenReturn("78460");

        when(appProperties.getProperty("FAMILY_BASE_SFO_ID")).thenReturn("78460");
        when(appProperties.getProperty("FAMILY_BASE_ADD_STR")).thenReturn("some_string");

        when(processor.triggerSMS(any(Long.class), anyString())).thenReturn("SMS_TRIGGERED");

        // Act
        String result = handler.process(document, "some_status");

        // Assert
        assertEquals("SMS_TRIGGERED", result);
    }

    @Test
    public void testProcessWithNonMatchingSFOID() throws Exception {
        // Arrange
        Document document = mock(Document.class);
        NodeList sfoIdNodes = mock(NodeList.class);

        when(document.evaluate("//EDR_CPF//CHANGE_DATA_FEATURES//SFO_ID", document, any())).thenReturn(sfoIdNodes);
        when(sfoIdNodes.getLength()).thenReturn(1);

        when(appProperties.getProperty("FAMILY_BASE_SFO_ID")).thenReturn("non_matching_id");

        // Act
        String result = handler.process(document, "some_status");

        // Assert
        assertEquals("P", result);
    }

    @Test
    public void testProcessWithNoSFOID() throws Exception {
        // Arrange
        Document document = mock(Document.class);
        NodeList sfoIdNodes = mock(NodeList.class);

        when(document.evaluate("//EDR_CPF//CHANGE_DATA_FEATURES//SFO_ID", document, any())).thenReturn(sfoIdNodes);
        when(sfoIdNodes.getLength()).thenReturn(0);

        // Act
        String result = handler.process(document, "some_status");

        // Assert
        assertEquals("P", result);
    }

    @Test
    public void testProcessWithNoEffectiveDateTag() throws Exception {
        // Arrange
        Document document = mock(Document.class);
        NodeList sfoIdNodes = mock(NodeList.class);
        Node sfoIdNode = mock(Node.class);
        NodeList childNodes = mock(NodeList.class);

        when(document.evaluate("//EDR_CPF//CHANGE_DATA_FEATURES//SFO_ID", document, any())).thenReturn(sfoIdNodes);
        when(sfoIdNodes.getLength()).thenReturn(1);
        when(sfoIdNodes.item(0)).thenReturn(sfoIdNode);

        when(sfoIdNode.hasChildNodes()).thenReturn(true);
        when(sfoIdNode.getChildNodes()).thenReturn(childNodes);
        when(childNodes.getLength()).thenReturn(1);
        when(childNodes.item(0).getNodeName()).thenReturn("SFO_ID");
        when(childNodes.item(0).getTextContent()).thenReturn("78460");

        when(appProperties.getProperty("FAMILY_BASE_SFO_ID")).thenReturn("78460");

        // Act
        String result = handler.process(document, "some_status");

        // Assert
        assertEquals("P", result);
    }

    @Test
    public void testProcessWithException() throws Exception {
        // Arrange
        Document document = mock(Document.class);

        when(document.evaluate("//EDR_CPF//CHANGE_DATA_FEATURES//SFO_ID", document, any()))
                .thenThrow(new RuntimeException("Test Exception"));

        // Act
        String result = handler.process(document, "some_status");

        // Assert
        assertEquals("P", result);
        // Additionally, you might want to assert the log messages or other behaviors during exception handling
    }
}
