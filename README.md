package com.verizon.onemsg.omppservice.receiver;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;
import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.service.ActivateProcessor;
import com.verizon.onemsg.omppservice.util.XmlUtil;

@Service	
public class MDNActivateReceiver {
	private Logger log = LogManager.getLogger(MDNActivateReceiver.class.getName());
	
	//@Autowired
	ActivateProcessor processor;
	
	public MDNActivateReceiver(@Lazy ActivateProcessor processor){
		this.processor = processor;
	}
	
	 public void onMessage(String message) {
			String status = null;
			try {
				//String txtMsg = ((TextMessage) message).getText();
				String txtMsg = message;
				log.debug("Request XML:" + txtMsg);
				Document doc = XmlUtil.loadXMLFromString(txtMsg);
				
				status = processor.processActivate(doc, AppConstants.Processes.ACTIVATION);
					log.info("ActivateProcessor completed with status :: " + status);				
										
				
			} catch (Throwable e) {
				
				log.error("Vision Activities", "CustActivityActivateMDB", "onMessage", e.getMessage(), "Error", e);			
			}

}

}
