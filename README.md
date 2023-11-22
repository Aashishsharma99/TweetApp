package com.verizon.onemsg.omppservice.receiver;

import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Component;

import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import com.vzw.cc.util.Encoder;

@Component
public class BatchDisconnectIBMQReceiver implements MessageListener {
	
private Logger log = LogManager.getLogger(BatchMDNDisconnectReceiver.class.getName());
	
	MDNCleanupService mdnCleanupService;
	
	public BatchDisconnectIBMQReceiver(@Lazy MDNCleanupService mdnCleanupService){
		this.mdnCleanupService = mdnCleanupService;
	}

	@Override
	public void onMessage(Message message) {
		String xmlMsg = null;
		TextMessage txtMsg = null;
		try {
			int deliveryCount = message.getIntProperty("JMSXDeliveryCount");
			log.debug("deliveryCount : " + deliveryCount);
			txtMsg = (TextMessage) message;
			xmlMsg = txtMsg.getText();
			log.debug("Request XML : " + xmlMsg);
			if (xmlMsg != null) {
				xmlMsg = Encoder.makeXmlValid(xmlMsg);
				mdnCleanupService.doDisconnectMDNProcessing(xmlMsg);
				log.debug("End BatchMDNDisconnectMDB");
			}
		}catch (Exception e) {
			log.error("OMPPS", "BatchMDNDisconnectMDB", "BatchMDNDisconnectMDB", "onMessage", e.getMessage(), "ERROR", e);
		}finally {
			 log.debug("finally block");

		}


		
	}

}
