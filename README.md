package com.verizon.onemsg.omppservice.receiver;

import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;

import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.service.ActivateProcessor;
import com.verizon.onemsg.omppservice.util.XmlUtil;

@Service
public class MDNActivateIBMReceiver implements MessageListener{
	private Logger log = LogManager.getLogger(MDNActivateIBMReceiver.class.getName());
	
	ActivateProcessor processor;
		
	public MDNActivateIBMReceiver(@Lazy ActivateProcessor processor){
		this.processor = processor;
	}
	
	
	@Override
	public void onMessage(Message message) {
		String status = null;
		try {
			String txtMsg = ((TextMessage) message).getText();
			log.debug("Request XML:" + txtMsg);
			Document doc = XmlUtil.loadXMLFromString(txtMsg);
			
			status = processor.processActivate(doc,  AppConstants.Processes.ACTIVATION);
			log.info("ActivateProcessor completed with status :: " + status);				
									
			
		} catch (Throwable e) {
			
			log.error("Vision Activities", "CustActivityActivateMDB", "onMessage", e.getMessage(), "Error", e);			
		}
	}

}
