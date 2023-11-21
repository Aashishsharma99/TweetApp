import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.Date;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.test.util.ReflectionTestUtils;

class CommonAuditDaoTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    private CommonAuditDao commonAuditDao;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        commonAuditDao = new CommonAuditDao(rabbitTemplate);
        ReflectionTestUtils.setField(commonAuditDao, "appId", "testAppId");
        ReflectionTestUtils.setField(commonAuditDao, "pcAdminDateFormat", "MMM-dd-yyyy HH:mm:ss");
        ReflectionTestUtils.setField(commonAuditDao, "auditExchange", "testAuditExchange");
        ReflectionTestUtils.setField(commonAuditDao, "pcRoutingKey", "testPcRoutingKey");
    }

    @Test
    void createAuditMap_ShouldCreateAuditInfo() {
        // Arrange
        String reqXML = "<test>data</test>";
        String clientId = "testClientId";
        String service = "testService";

        // Act
        AuditInfo auditInfo = commonAuditDao.createAuditMap(reqXML, clientId, service);

        // Assert
        assertNotNull(auditInfo);
        assertEquals(clientId, auditInfo.getClient_id());
        assertEquals(reqXML, auditInfo.getReqInput());
        assertEquals("testAppId", auditInfo.getApp_id());
        assertEquals(service, auditInfo.getService());
        assertNotNull(auditInfo.getServer());
        assertNotNull(auditInfo.getAuditId());
        assertNotNull(auditInfo.getAudit_TS());
    }

    @Test
    void updateAuditMap_WithDetails_ShouldUpdateAuditInfo() {
        // Arrange
        AuditInfo auditInfo = new AuditInfo(123L);
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
    void updateAuditMap_WithStatus_ShouldUpdateAuditInfo() throws Exception {
        // Arrange
        AuditInfo auditInfo = new AuditInfo(123L);
        String status = "testStatus";

        // Act
        commonAuditDao.updateAuditMap(auditInfo, status);

        // Assert
        assertEquals(status, auditInfo.getStatus());
        assertNotNull(auditInfo.getResponse_TS());
        assertNotNull(auditInfo.getResponse_time());
    }

    @Test
    void submitAduitMessage_ShouldSendMessageToRabbitMQ() {
        // Arrange
        AuditInfo auditInfo = new AuditInfo(123L);

        // Act
        commonAuditDao.submitAduitMessage(auditInfo);

        // Assert
        verify(rabbitTemplate, times(1)).convertAndSend(
                eq("testAuditExchange"),
                eq("testPcRoutingKey"),
                any(byte[].class));
    }

    // Add more test cases as needed, including edge cases and exception scenarios.
}
