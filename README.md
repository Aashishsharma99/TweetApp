import com.verizon.onemsg.omppservice.properties.AppProperties;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.mockito.Mockito.when;

class CommonUtilsTest {

    @Mock
    private AppProperties appProperties;

    @InjectMocks
    private CommonUtils commonUtils;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    void connectToSunMsgServer_whenEnvironmentIsNotProd_shouldReturnFalse() {
        // Arrange
        when(appProperties.getProperty("PROD_EAST_MAILHOST_PREFIX")).thenReturn("east");
        when(appProperties.getProperty("PROD_WEST_MAILHOST_PREFIX")).thenReturn("west");

        // Act
        boolean result = commonUtils.connectToSunMsgServer("east-mailhost");

        // Assert
        assertFalse(result);
    }

    @Test
    void connectToSunMsgServer_whenEnvironmentIsProdAndConnectEnabledForEastMailhost_shouldReturnTrue() {
        // Arrange
        when(appProperties.getProperty("PROD_EAST_MAILHOST_PREFIX")).thenReturn("east");
        when(appProperties.getProperty("PROD_WEST_MAILHOST_PREFIX")).thenReturn("west");
        when(appProperties.getProperty("CONNECT_TO_SUN_MESSAGE_SERVER_EAST", "OFF")).thenReturn("ON");
        when(appProperties.getProperty("CONNECT_TO_SUN_MESSAGE_SERVER_WEST", "OFF")).thenReturn("OFF");

        // Act
        boolean result = commonUtils.connectToSunMsgServer("east-mailhost");

        // Assert
        assertTrue(result);
    }

    @Test
    void connectToSunMsgServer_whenEnvironmentIsProdAndConnectEnabledForWestMailhost_shouldReturnTrue() {
        // Arrange
        when(appProperties.getProperty("PROD_EAST_MAILHOST_PREFIX")).thenReturn("east");
        when(appProperties.getProperty("PROD_WEST_MAILHOST_PREFIX")).thenReturn("west");
        when(appProperties.getProperty("CONNECT_TO_SUN_MESSAGE_SERVER_EAST", "OFF")).thenReturn("OFF");
        when(appProperties.getProperty("CONNECT_TO_SUN_MESSAGE_SERVER_WEST", "OFF")).thenReturn("ON");

        // Act
        boolean result = commonUtils.connectToSunMsgServer("west-mailhost");

        // Assert
        assertTrue(result);
    }

    // Add more test cases for other scenarios as needed...
}
