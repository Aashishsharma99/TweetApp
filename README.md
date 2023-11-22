import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.jms.listener.DefaultMessageListenerContainer;
import org.springframework.jms.listener.MessageListenerContainer;
import org.springframework.jms.listener.adapter.MessageListenerAdapter;
import org.springframework.test.util.ReflectionTestUtils;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class OMPPIBMListenerTest {

    @Autowired
    private OMPPIBMListener omppIbmListener;

    @Autowired
    private MDNChangeIBMreceiver changeReceiver;

    @Autowired
    private MDNReassignIBMReceiver reassignReceiver;

    @Autowired
    private MDNTransferIBMReceiver transferReceiver;

    @Autowired
    private MDNActivateIBMReceiver activateReceiver;

    @Autowired
    private RealtimeDisconnectIBMQReceiver realtimeDisconnectReceiver;

    @Autowired
    private BatchDisconnectIBMQReceiver batchDisconnectReceiver;

    @Test
    void testMDNChangeListenerContainer() {
        assertListenerContainerProperties(omppIbmListener.getChangeRequestProcessorContainer(null), chnageRecieverDestination, changeReceiver, true);
    }

    @Test
    void testMDNReassignListenerContainer() {
        assertListenerContainerProperties(omppIbmListener.getReassignRequestProcessorContainer(null), reassignRecieverDestination, reassignReceiver, true);
    }

    @Test
    void testMDNTransferListenerContainer() {
        assertListenerContainerProperties(omppIbmListener.getTransferRequestProcessorContainer(null), transferRecieverDestination, transferReceiver, true);
    }

    @Test
    void testMDNActivateListenerContainer() {
        assertListenerContainerProperties(omppIbmListener.getActivateRequestProcessorContainer(null), activateRecieverDestination, activateReceiver, true);
    }

    @Test
    void testMDNRealtimeDisconnectListenerContainer() {
        assertListenerContainerProperties(omppIbmListener.getRealtimeDisconnectRequestProcessorContainer(null), realtimeRecieverDestination, realtimeDisconnectReceiver, true);
    }

    @Test
    void testMDNBatchDisconnectListenerContainer() {
        assertListenerContainerProperties(omppIbmListener.getBatchDisconnectRequestProcessorContainer(null), batchDisconnectRecieverDestination, batchDisconnectReceiver, true);
    }

    @Test
    void testMDNChangeListenerContainer_WithDisabledProperty() {
        assertListenerContainerDisabledProperty(omppIbmListener, "enableMdnChnageOmppIBMMQ");
    }

    @Test
    void testMDNReassignListenerContainer_WithDisabledProperty() {
        assertListenerContainerDisabledProperty(omppIbmListener, "enablemdnReassignOmppIBMMQ");
    }

    @Test
    void testMDNTransferListenerContainer_WithDisabledProperty() {
        assertListenerContainerDisabledProperty(omppIbmListener, "enableMdnTransferOmppIBMMQ");
    }

    @Test
    void testMDNActivateListenerContainer_WithDisabledProperty() {
        assertListenerContainerDisabledProperty(omppIbmListener, "enableMdnActivateOmppIBMMQ");
    }

    @Test
    void testMDNRealtimeDisconnectListenerContainer_WithDisabledProperty() {
        assertListenerContainerDisabledProperty(omppIbmListener, "enableRealtimeOmppIBMMQ");
    }

    @Test
    void testMDNBatchDisconnectListenerContainer_WithDisabledProperty() {
        assertListenerContainerDisabledProperty(omppIbmListener, "enableBatchOmppIBMMQ");
    }

    private void assertListenerContainerProperties(MessageListenerContainer container, String destinationName, Object messageListener, boolean autoStartup) {
        assertNotNull(container);
        assertTrue(container instanceof DefaultMessageListenerContainer);

        DefaultMessageListenerContainer defaultContainer = (DefaultMessageListenerContainer) container;

        assertEquals(100, defaultContainer.getConcurrency());
        assertEquals(-1, defaultContainer.getMaxMessagesPerTask());
        assertEquals(0, defaultContainer.getCacheLevel());
        assertTrue(defaultContainer.isSessionTransacted());
        assertEquals(Session.CLIENT_ACKNOWLEDGE, defaultContainer.getSessionAcknowledgeMode());
        assertEquals(destinationName, ReflectionTestUtils.getField(defaultContainer, "destinationName"));

        MessageListenerAdapter messageListenerAdapter = (MessageListenerAdapter) defaultContainer.getMessageListener();
        assertNotNull(messageListenerAdapter);
        assertEquals(messageListener, messageListenerAdapter.getDelegate());
        assertEquals(autoStartup, defaultContainer.isAutoStartup());
    }

    private void assertListenerContainerDisabledProperty(OMPPIBMListener listener, String propertyName) {
        // Set the property to false
        ReflectionTestUtils.setField(listener, propertyName, false);

        MessageListenerContainer container = null;
        switch (propertyName) {
            case "enableMdnChnageOmppIBMMQ":
                container = listener.getChangeRequestProcessorContainer(null);
                break;
            case "enablemdnReassignOmppIBMMQ":
                container = listener.getReassignRequestProcessorContainer(null);
                break;
            case "enableMdnTransferOmppIBMMQ":
                container = listener.getTransferRequestProcessorContainer(null);
                break;
            case "enableMdnActivateOmppIBMMQ":
                container = listener.getActivateRequestProcessorContainer(null);
                break;
            case "enableRealtimeOmppIBMMQ":
                container = listener.getRealtimeDisconnectRequestProcessorContainer(null);
                break;
            case "enableBatchOmppIBMMQ":
                container = listener.getBatchDisconnectRequestProcessorContainer(null);
                break;
        }

        assertNotNull(container);
        assertFalse(container.isAutoStartup());
    }
}
