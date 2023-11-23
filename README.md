import com.vzw.cc.valueobjects.AuditInfo;
import com.vzw.cc.valueobjects.TranLog;
import org.apache.xmlbeans.XmlObject;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
public class AuditInfoTest {

    @Test
    public void testConstructor() {
        AuditInfo auditInfo = new AuditInfo(1L);

        // Check if the uniqueId is set correctly
        assertEquals(1L, auditInfo.getUniqueId());
    }

    @Test
    public void testSetGeneralInfoWithRequestTS() {
        AuditInfo auditInfo = new AuditInfo(1L);

        // Set general info with request timestamp
        auditInfo.setGeneralInfo("APP1", "Service1", "2023-01-01T12:00:00");

        // Check if the general info is set correctly
        assertEquals("APP1", auditInfo.getAppl_id());
        assertEquals("Service1", auditInfo.getService_name());
        assertEquals("2023-01-01T12:00:00", auditInfo.getRequest_TS());
    }

    @Test
    public void testSetGeneralInfoWithoutRequestTS() {
        AuditInfo auditInfo = new AuditInfo(1L);

        // Set general info without request timestamp
        auditInfo.setGeneralInfo("APP2", "Service2");

        // Check if the general info is set correctly
        assertEquals("APP2", auditInfo.getAppl_id());
        assertEquals("Service2", auditInfo.getService_name());
        assertNull(auditInfo.getRequest_TS());
    }

    @Test
    public void testSetBusinessInfo() {
        AuditInfo auditInfo = new AuditInfo(1L);
        XmlObject mockAuditLog = /* Create a mock XmlObject for testing */;

        // Set business info
        auditInfo.setBusinessInfo(mockAuditLog);

        // Check if the business info is set correctly
        assertEquals(mockAuditLog, auditInfo.getBusinessInfo());
    }

    @Test
    public void testAddTransWithLongClassName() {
        AuditInfo auditInfo = new AuditInfo(1L);

        // Add a transaction with a long class name
        auditInfo.addTrans("Type1", "VeryLongClassNameThatExceedsMaxLength", "Method1", "Request", "Response", "2023-01-01T12:00:00", 100L, "System1", "Success");

        // Check if the class name is truncated to the maximum length
        List<TranLog> tranLogs = auditInfo.getTranLogs();
        assertEquals("...ClassNameThatExceedsMaxLength", tranLogs.get(0).get(TranLog.class_name));
    }

    @Test
    public void testAddTransAndCheckListNotNull() {
        AuditInfo auditInfo = new AuditInfo(1L);

        // Add a transaction
        auditInfo.addTrans("Type2", "Class2", "Method2", "Request2", "Response2", "2023-01-01T12:01:00", 150L, "System2", "Failure");

        // Check if the transaction list is not null
        assertNotNull(auditInfo.getTranLogs());
    }

    // Add more test cases for other methods in AuditInfo class...

    @Test
    public void testAddTransWithNullValues() {
        AuditInfo auditInfo = new AuditInfo(1L);

        // Add a transaction with null values
        auditInfo.addTrans(null, null, null, null, null, null, null, null, null);

        // Check if the transaction is added with null values
        List<TranLog> tranLogs = auditInfo.getTranLogs();
        assertNotNull(tranLogs);
        assertEquals(1, tranLogs.size());
        TranLog addedTrans = tranLogs.get(0);
        assertNull(addedTrans.get(TranLog.trns_type));
        assertNull(addedTrans.get(TranLog.class_name));
        assertNull(addedTrans.get(TranLog.method_name));
        assertNull(addedTrans.get(TranLog.req_input));
        assertNull(addedTrans.get(TranLog.resp_output));
        assertNull(addedTrans.get(TranLog.req_ts));
        assertNull(addedTrans.get(TranLog.response_time));
        assertNull(addedTrans.get(TranLog.ext_system));
        assertNull(addedTrans.get(TranLog.status));
    }
    
    // Add more test cases for other methods in AuditInfo class...
}
