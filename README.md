import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.boot.test.context.SpringBootTest;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.doThrow;

@SpringBootTest
@ExtendWith(MockitoExtension.class)
class MDNReassignReceiverTest {

    @InjectMocks
    private MDNReassignReceiver receiver;

    @Mock
    private MDNReassignHandler handlerMock;

    @Test
    void testOnMessage() throws Exception {
        // Arrange
        String xmlMessage = "<xml>Your XML Message here</xml>";

        // Act and Assert
        // In this example, we assume that MDNReassignHandler.process will throw an exception
        doThrow(new Exception("Simulated Exception")).when(handlerMock).process(any(), any());
        
        // Use onlyThenThrow to specify the exception to be thrown
        org.junit.jupiter.api.Assertions.assertThrows(Exception.class, () -> {
            receiver.onMessage(xmlMessage);
        });
    }

    // Additional test cases can be added as needed
}
