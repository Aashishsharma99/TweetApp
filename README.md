import com.vzw.cc.valueobjects.AuditInfo;
import com.vzw.cc.valueobjects.TranLog;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.verify;

@ExtendWith(MockitoExtension.class)
class AuditInfoTest {

    @InjectMocks
    private AuditInfo auditInfo;

    @Mock
    private TranLog tranLogMock;

    @Test
    void testAddTrans_withValidParameters_shouldCreateTranLogAndAddToLogs() {
        // Given
        String transType = "type";
        String className = "com.example.ClassName";
        String methodName = "method";
        String req = "request";
        String resp = "response";
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, className, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.req_id, any());
        verify(tranLogMock).put(TranLog.seq_no, any(Long.class));
        verify(tranLogMock).put(TranLog.trns_type, transType);
        verify(tranLogMock).put(TranLog.class_name, "ClassName");
        verify(tranLogMock).put(TranLog.method_name, methodName);
        verify(tranLogMock).put(TranLog.req_input, req);
        verify(tranLogMock).put(TranLog.resp_output, resp);
        verify(tranLogMock).put(TranLog.req_ts, reqTS);
        verify(tranLogMock).put(TranLog.response_time, respTime);
        verify(tranLogMock).put(TranLog.ext_system, extSystem);
        verify(tranLogMock).put(TranLog.status, status);
        verify(auditInfo).addTrans(tranLogMock);
    }

    @Test
    void testAddTrans_withNullTransLog_shouldNotAddToLogs() {
        // When
        auditInfo.addTrans((TranLog) null);

        // Then
        verify(auditInfo).addTrans(any(TranLog.class)); // Verify that addTrans method is called with any TranLog instance
    }

    @Test
    void testAddTrans_withNullClassName_shouldCreateTranLogWithEmptyClassName() {
        // Given
        String transType = "type";
        String methodName = "method";
        String req = "request";
        String resp = "response";
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, null, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.class_name, ""); // Verify that class_name is set to an empty string
    }

    @Test
    void testAddTrans_withLongClassName_shouldTruncateClassName() {
        // Given
        String transType = "type";
        String className = "com.example.really.long.class.name";
        String methodName = "method";
        String req = "request";
        String resp = "response";
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, className, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.class_name, "class.name"); // Verify that class_name is truncated
    }

    @Test
    void testAddTrans_withMaxLengthReq_shouldTruncateReqInput() {
        // Given
        String transType = "type";
        String className = "com.example.ClassName";
        String methodName = "method";
        String req = "a".repeat(AuditInfo.MAX_LENGTH + 1); // Exceeds the maximum length
        String resp = "response";
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, className, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.req_input, req.substring(0, AuditInfo.MAX_LENGTH - 1)); // Verify truncation
    }

    @Test
    void testAddTrans_withNullReq_shouldNotTruncateReqInput() {
        // Given
        String transType = "type";
        String className = "com.example.ClassName";
        String methodName = "method";
        String req = null; // Null request
        String resp = "response";
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, className, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.req_input, null); // Verify no truncation
    }

    @Test
    void testAddTrans_withLongResp_shouldTruncateRespOutput() {
        // Given
        String transType = "type";
        String className = "com.example.ClassName";
        String methodName = "method";
        String req = "request";
        String resp = "a".repeat(AuditInfo.MAX_LENGTH + 1); // Exceeds the maximum length
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, className, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.resp_output, resp.substring(0, AuditInfo.MAX_LENGTH - 1)); // Verify truncation
    }

    @Test
    void testAddTrans_withNullResp_shouldNotTruncateRespOutput() {
        // Given
        String transType = "type";
        String className = "com.example.ClassName";
        String methodName = "method";
        String req = "request";
        String resp = null; // Null response
        String reqTS = "timestamp";
        Long respTime = 100L;
        String extSystem = "external";
        String status = "success";

        // When
        auditInfo.addTrans(transType, className, methodName, req, resp, reqTS, respTime, extSystem, status);

        // Then
        verify(tranLogMock).put(TranLog.resp_output, null); // Verify no truncation
    }

    @Test
    void testAddTrans_withValidTranLog_shouldAddToLogs() {
        // Given
        TranLog validTranLog = new TranLog();

        // When
        auditInfo.addTrans(validTranLog);

        // Then
        verify(auditInfo).addTrans(validTranLog); // Verify that addTrans method is called with the provided TranLog
    }

    @Test
    void testAddTransLog_withNullTranLog_shouldNotAddToLogs() {
        // When
        auditInfo.addTrans((TranLog) null);

        // Then
        List<TranLog> tranLogs = auditInfo.getTranLogs();
        assertNotNull(tranLogs);
        assertEquals(0, tranLogs.size()); // Verify that no TranLog is added
    }

    // Add more test cases as needed

}
}
