package com.verizon.onemsg.omppservice.receiver;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;

import com.verizon.onemsg.omppservice.handler.MDNReassignHandler;
import com.verizon.onemsg.omppservice.service.VisionAlertsProcessor;

@Service
public class MDNReassignReceiver {
	private Logger log = LogManager.getLogger(MDNReassignReceiver.class.getName());
	
	@Autowired
	MDNReassignHandler handler;
	
	
	public void onMessage(String msg) {
		
		
		String processCode = null;		
		
		int deliveryCount = 0;
		
		
		try {
			String strMsg =msg;
			log.debug("Request XML:" + strMsg);		
			strMsg = strMsg.replaceAll("[^\\x20-\\x7e]", "");
			Document visionDoc = VisionAlertsProcessor.loadXMLFromString(strMsg);			
			processCode = handler.process(visionDoc, processCode);							
			
		} catch (Exception e) {
			log.error("CustActivityReassign", "CustActivityReassignMDB", "onMessage", e.getMessage(), "ERROR", e);			
		} finally {
			
		}
	}

}
