import com.verizon.onemsg.omppservice.beans.AuditInfo;
import com.verizon.onemsg.omppservice.beans.BOSAccountBean;
import com.verizon.onemsg.omppservice.util.CommonUtils;
import com.verizon.onemsg.omppservice.util.HttpCall;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyInt;
import static org.mockito.Mockito.*;

@ExtendWith({MockitoExtension.class, SpringExtension.class})
class CommonUtilsTest {

    @Mock
    private HttpCall httpCall;

    @Value("${BOS_AUTH_USERNAME}")
    private String bosUserName;

    @Value("${BOS_AUTH_PASSWORD}")
    private String bosPassword;

    @Value("${BOS_PC_SERVICES_HTTP_TIMEOUT}")
    private String bosBosPcHttpTimeout;

    @Value("${BOS_PC_SERVICE_ACTIVATE_ENDPOINT}")
    private String bosPcServiceActivateEndpoint;

    @Value("${BOS_PC_SERVICE_DEACTIVATE_ENDPOINT}")
    private String bosPcServiceDeactivateEndPoint;

    @Value("${APP_ID}")
    private String appId;

    @Value("${ENV}")
    private static String environment;

    @InjectMocks
    private CommonUtils commonUtils;

    @Test
    void testMakeCallToBOSPreferenceCenter() {
        // Arrange
        String activityType = "ACTIVATE";
        BOSAccountBean bean = new BOSAccountBean(); // Provide necessary data for BOSAccountBean
        AuditInfo auditInfo = new AuditInfo(); // Provide necessary data for AuditInfo

        // Mocking
        when(httpCall.submitJSONToUrl(any(), any(), anyInt(), any())).thenReturn("200");

        // Act
        String result = commonUtils.makeCallToBOSPreferenceCenter(activityType, bean, auditInfo);

        // Assert
        assertEquals("P", result);
        // Add more assertions or verifications based on your requirements
    }

    @Test
    void testConnectToSunMsgServer() {
        // Arrange
        String mailhost = "your_mailhost_here"; // Provide a valid mailhost

        // Mocking
        when(httpCall.submitJSONToUrl(any(), any(), anyInt(), any())).thenReturn("200");

        // Act
        boolean result = commonUtils.connectToSunMsgServer(mailhost);

        // Assert
        // Add assertions based on your requirements
    }
}
