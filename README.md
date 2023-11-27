package com.verizon.onemsg.omppservice.handler;

import javax.annotation.PostConstruct;
import javax.xml.xpath.XPathFactory;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.dao.CommonAuditDao;
import com.verizon.onemsg.omppservice.receiver.MDNTransferReceiver;
import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import com.verizon.onemsg.omppservice.service.VisionAlertsProcessor;
import com.verizon.onemsg.omppservice.util.XmlUtil;
import com.vzw.cc.valueobjects.AuditInfo;

@Service
public class MDBtransferHandler {
	
	private Logger log = LogManager.getLogger(MDBtransferHandler.class.getName());

	VisionAlertsProcessor processor = null;
	
	@Autowired
	CommonAuditDao auditdao;
	
	
	public MDBtransferHandler(@Lazy VisionAlertsProcessor processor){
		this.processor = processor;
	}
	
	public String process(Document transferDocument, String status){
		
		String processStatus = null;		
		AuditInfo auditInfo = null;		
		long time = System.currentTimeMillis();
		if(status == null)
			status = "A";
		
		try {			
					
			String billingSystemId = (VisionAlertsProcessor.getNode(transferDocument,  
																	XPathFactory.newInstance().newXPath(), 
																	"//EDR_CPF//BILLING_SYSTEM_ID//text()"));
			//Audit Entries	
			auditInfo = auditdao.createAuditMap(XmlUtil.getXml(transferDocument), AppConstants.VISION_CLIENTID, "MDN_TRANSFER");
			auditInfo.setTrns_type("TRN");
			auditInfo.setRequest_TS(auditdao.getAuditDate());
			auditInfo.setStatus(status);
			auditInfo.setFlexField1(processor.getBillSystem(Integer.parseInt(billingSystemId)));		
			
			Node node = transferDocument.getFirstChild();
			NodeList nodeList = node.getChildNodes();
			
			for (int i=0; i < nodeList.getLength(); i++) {
				node = nodeList.item(i);
	            if (node.getNodeName().equals("TRANSFER"))
	            	processStatus = processor.processTransfer(auditInfo, node);
	        }		
		} catch (Exception e) {			
			log.error("CustActivityTransfer", "MDNTransferBean", "process", e.getMessage(), "ERROR", e);
			processStatus = status;
		}  finally{	
			if (auditInfo != null) {
				auditInfo.setResponse_TS(auditdao.getAuditDate());
				auditInfo.setResponse_time(System.currentTimeMillis() - time);
				auditInfo.setStatus(processStatus);
				auditdao.submitAduitMessage(auditInfo);
			}
		}
		return processStatus;
	}
}



package com.verizon.onemsg.omppservice.handler;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.Spy;
import org.mockito.junit.MockitoJUnitRunner;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;
import org.w3c.dom.Document;
import org.w3c.dom.NodeList;
import org.w3c.dom.Node;
import com.verizon.onemsg.omppservice.dao.CommonAuditDao;
import com.verizon.onemsg.omppservice.service.VisionAlertsProcessor;
import com.vzw.cc.valueobjects.AuditInfo;

@RunWith(MockitoJUnitRunner.class)
@SpringJUnitConfig
class MDBtransferHandlerTest {
	
	@Spy
	@InjectMocks
	MDBtransferHandler mdbtransferHandler;
	
	@Mock
	VisionAlertsProcessor processor;
	@Mock
	Document transferDocument;
	@Mock
	CommonAuditDao auditdaoMock;
	@Mock
	AuditInfo auditInfo;
	@Mock
	NodeList nodeList;
	@Mock
	Node node;
	
	@Test
	void testMDBtransferHandler() {
		MDBtransferHandler mdbtransferHandler = new MDBtransferHandler(processor);
		
	}

	@Test
	void testProcess() {
		int i =0;
		mdbtransferHandler.auditdao = auditdaoMock;
		when(auditdaoMock.createAuditMap(Mockito.anyString(), Mockito.anyString(), Mockito.anyString())).thenReturn(auditInfo);
		
		when(processor.getBillSystem(Mockito.anyInt())).thenReturn("FlexField1");
		when(transferDocument.getFirstChild()).thenReturn(node);
		when(node.getChildNodes()).thenReturn(nodeList);
		
		
		
		mdbtransferHandler.process(transferDocument, "OK");
		
		mdbtransferHandler.process(transferDocument, null);
	}

}
