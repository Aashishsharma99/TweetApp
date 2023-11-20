import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.*;
import static org.mockito.Mockito.*;

import java.text.ParseException;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.util.SerializationUtils;

import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.vzw.cc.util.UniqueIdGenerator;
import com.vzw.cc.valueobjects.AuditInfo;

class CommonAuditDaoTest {

    @Mock
    private RabbitTemplate pcauditRabbitTemplate;

    @Mock
    private AppProperties appProperties;

    @InjectMocks
    private CommonAuditDao commonAuditDao;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    void testCreateAuditMap() {
        // Arrange
        String reqXML = "<xml>test</xml>";
        String clientId = "testClientId";
        String service = "testService";

        // Mocking
        when(appProperties.getProperty(anyString(), anyString())).thenReturn("MMM-dd-yyyy HH:mm:ss");
        when(pcauditRabbitTemplate.convertAndSend(anyString(), anyString(), any(byte[].class))).thenReturn(null);

        // Act
        AuditInfo auditInfo = commonAuditDao.createAuditMap(reqXML, clientId, service);

        // Assert
        assertNotNull(auditInfo);
        assertEquals(reqXML, auditInfo.getReqInput());
        assertEquals(clientId, auditInfo.getClient_id());
        assertEquals("APP_ID", auditInfo.getApp_id());
        assertEquals(service, auditInfo.getService());
        assertNotNull(auditInfo.getServer());
    }

    @Test
    void testUpdateAuditMap() {
        // Arrange
        AuditInfo auditInfo = new AuditInfo();
        String mdn = "testMdn";
        String custId = "testCustId";
        String accNo = "testAccNo";
        String sfoId = "testSfoId";

        // Act
        commonAuditDao.updateAuditMap(auditInfo, mdn, custId, accNo, sfoId);

        // Assert
        assertEquals(custId, auditInfo.getCust_id());
        assertEquals(mdn, auditInfo.getNotification_address());
        assertEquals(accNo, auditInfo.getAcct_no());
        assertEquals(sfoId, auditInfo.getTrns_type());
    }

    @Test
    void testUpdateAuditMapWithStatus() throws ParseException {
        // Arrange
        AuditInfo auditInfo = new AuditInfo();
        String status = "testStatus";

        // Mocking
        when(appProperties.getProperty(anyString(), anyString())).thenReturn("MMM-dd-yyyy HH:mm:ss");

        // Act
        commonAuditDao.updateAuditMap(auditInfo, status);

        // Assert
        assertEquals(status, auditInfo.getStatus());
        assertNotNull(auditInfo.getResponse_TS());
        assertNotNull(auditInfo.getResponse_time());
    }

    @Test
    void testSubmitAuditMessage() {
        // Arrange
        AuditInfo auditInfo = new AuditInfo();

        // Mocking
        when(pcauditRabbitTemplate.convertAndSend(anyString(), anyString(), any(byte[].class))).thenReturn(null);

        // Act
        commonAuditDao.submitAduitMessage(auditInfo);

        // Assert
        verify(pcauditRabbitTemplate, times(1)).convertAndSend(anyString(), anyString(), any(byte[].class));
    }

    @Test
    void testSubmitAuditMessageWithStatus() {
        // Arrange
        AuditInfo auditInfo = new AuditInfo();
        String status = "testStatus";

        // Mocking
        when(pcauditRabbitTemplate.convertAndSend(anyString(), anyString(), any(byte[].class))).thenReturn(null);
        when(appProperties.getProperty(anyString(), anyString())).thenReturn("MMM-dd-yyyy HH:mm:ss");

        // Act
        commonAuditDao.submitAduitMessage(auditInfo, status);

        // Assert
        verify(pcauditRabbitTemplate, times(1)).convertAndSend(anyString(), anyString(), any(byte[].class));
    }

    @Test
    void testEncodeObj() throws Exception {
        // Arrange
        Object obj = new Object();

        // Act
        String encodedMsg = commonAuditDao.encodeObj(obj);

        // Assert
        assertNotNull(encodedMsg);
    }

    @Test
    void testGetAuditDate() {
        // Mocking
        when(appProperties.getProperty(anyString(), anyString())).thenReturn("MMM-dd-yyyy HH:mm:ss");

        // Act
        String auditDate = commonAuditDao.getAuditDate();

        // Assert
        assertNotNull(auditDate);
    }
}
