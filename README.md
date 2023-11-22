import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import com.verizon.onemsg.omppservice.receiver.BatchMDNDisconnectReceiver;
import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.core.Appender;
import org.apache.logging.log4j.core.LogEvent;
import org.apache.logging.log4j.core.Logger;
import org.apache.logging.log4j.core.appender.AbstractAppender;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

@SpringBootTest
@ExtendWith(SpringExtension.class)
class BatchMDNDisconnectReceiverTest {

    @Autowired
    private BatchMDNDisconnectReceiver batchMDNDisconnectReceiver;

    private TestSetup testSetup;

    @BeforeEach
    void setUp() {
        testSetup = new TestSetup();
    }

    @Test
    void testOnMessage() {
        // Arrange
        String xmlMsg = "<xml>your_xml_here</xml>";

        // Act
        batchMDNDisconnectReceiver.onMessage(xmlMsg);

        // Assert
        testSetup.verifyDoDisconnectMDNProcessingCalledWith(xmlMsg);
        testSetup.verifyLogMessage("End BatchMDNDisconnectMDB");
    }

    @Test
    void testOnMessage2() {
        // Act
        batchMDNDisconnectReceiver.onMessage(null);

        // Assert
        testSetup.verifyDoDisconnectMDNProcessingNotCalled();
        testSetup.verifyLogMessage("### OM request received as string:null");
    }

    // Add more tests...

    private static class TestSetup {

        private MDNCleanupService mdnCleanupServiceMock;
        private Appender appender;

        TestSetup() {
            mdnCleanupServiceMock = mock(MDNCleanupService.class);
            batchMDNDisconnectReceiver = new BatchMDNDisconnectReceiver(mdnCleanupServiceMock);

            // Capture log messages for verification
            Logger logger = (Logger) LogManager.getLogger(BatchMDNDisconnectReceiver.class);
            appender = mockAppender(logger);
            logger.addAppender(appender);
        }

        void verifyDoDisconnectMDNProcessingCalledWith(String xmlMsg) {
            verify(mdnCleanupServiceMock, times(1)).doDisconnectMDNProcessing(xmlMsg);
        }

        void verifyDoDisconnectMDNProcessingNotCalled() {
            verifyZeroInteractions(mdnCleanupServiceMock);
        }

        void verifyLogMessage(String expectedLogMessage) {
            ArgumentCaptor<LogEvent> logCaptor = ArgumentCaptor.forClass(LogEvent.class);
            verify(appender, atLeastOnce()).append(logCaptor.capture());
            assertTrue(logCaptor.getAllValues().stream().anyMatch(logEvent ->
                    logEvent.getMessage().getFormattedMessage().contains(expectedLogMessage)));
        }

        private Appender mockAppender(Logger logger) {
            Appender mockAppender = mock(AbstractAppender.class);
            when(mockAppender.getName()).thenReturn("MockAppender");
            doAnswer(invocation -> {
                LogEvent event = invocation.getArgument(0);
                logger.log(event);
                return null;
            }).when(mockAppender).append(any());

            return mockAppender;
        }
    }
}
