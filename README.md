import com.verizon.onemsg.omppservice.beans.BOSAccountBean;
import com.vzw.cc.valueobjects.AuditInfo;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.TestPropertySource;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.*;
import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class)
@SpringBootTest
@TestPropertySource(properties = {
        "BOS_AUTH_USERNAME=testUser",
        "BOS_AUTH_PASSWORD=testPassword",
        "BOS_PC_SERVICES_HTTP_TIMEOUT=5000",
        "BOS_PC_SERVICE_ACTIVATE_ENDPOINT=http://activateEndpoint",
        "BOS_PC_SERVICE_DEACTIVATE_ENDPOINT=http://deactivateEndpoint",
        "APP_ID=testAppId",
        "ENV=testEnvironment"
})
class CommonUtilsTest {

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
    private String environment;

    @Mock
    private HttpCall httpCall;

    @Mock
    private AppProperties props;

    @InjectMocks
    private CommonUtils commonUtils;

    @Test
    void makeCallToBOSPreferenceCenter_activate_shouldReturnStatusP() throws Exception {
        // Arrange
        BOSAccountBean bean = new BOSAccountBean();
        AuditInfo auditInfo = new AuditInfo();
        when(httpCall.submitJSONToUrl(anyString(), anyString(), anyInt(), anyString())).thenReturn("200");

        // Act
        String result = commonUtils.makeCallToBOSPreferenceCenter("ACTIVATE", bean, auditInfo);

        // Assert
        assertEquals("P", result);
    }

    @Test
    void makeCallToBOSPreferenceCenter_deactivate_shouldReturnStatusP() throws Exception {
        // Arrange
        BOSAccountBean bean = new BOSAccountBean();
        AuditInfo auditInfo = new AuditInfo();
        when(httpCall.submitJSONToUrl(anyString(), anyString(), anyInt(), anyString())).thenReturn("200");

        // Act
        String result = commonUtils.makeCallToBOSPreferenceCenter("DEACTIVATE", bean, auditInfo);

        // Assert
        assertEquals("P", result);
    }

    @Test
    void makeCallToBOSPreferenceCenter_httpCallReturnsNon200_shouldReturnStatusF() throws Exception {
        // Arrange
        BOSAccountBean bean = new BOSAccountBean();
        AuditInfo auditInfo = new AuditInfo();
        when(httpCall.submitJSONToUrl(anyString(), anyString(), anyInt(), anyString())).thenReturn("404");

        // Act
        String result = commonUtils.makeCallToBOSPreferenceCenter("ACTIVATE", bean, auditInfo);

        // Assert
        assertEquals("F", result);
    }

    // Add more test cases as needed
}
