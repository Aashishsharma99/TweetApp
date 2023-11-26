package com.verizon.onemsg.omppservice.handler;

import javax.xml.xpath.XPath;
import javax.xml.xpath.XPathConstants;
import javax.xml.xpath.XPathExpression;
import javax.xml.xpath.XPathFactory;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;
import org.w3c.dom.NodeList;

import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.verizon.onemsg.omppservice.receiver.MDNChangeReceiver;
import com.verizon.onemsg.omppservice.service.CommonProcessor;
import com.verizon.onemsg.omppservice.service.VisionAlertsProcessor;

@Service
public class FamilyBaseSFOHandler {
	private Logger log = LogManager.getLogger(FamilyBaseSFOHandler.class.getName());
	
	@Autowired
	AppProperties appProps;
	
	@Autowired
	CommonProcessor processor;
	
	public String process(Document document, String status) {

		try {

			XPath xpath = XPathFactory.newInstance().newXPath();
			XPathExpression expr = xpath.compile("//EDR_CPF//CHANGE_DATA_FEATURES//SFO_ID");
			NodeList nodes = (NodeList) expr.evaluate(document, XPathConstants.NODESET);
			
			log.info("Inside FamilyBaseSFOBean::process()");			
		
			NodeList childNodes;
			
			if (nodes != null && nodes.getLength() > 0) {

				for (int i = 0; i < nodes.getLength(); i++) {
					if (nodes.item(i).hasChildNodes()) {
						childNodes = nodes.item(i).getChildNodes();
						for (int j = 0; childNodes != null && j < childNodes.getLength(); j++) {
							if ("SFO_ID".equalsIgnoreCase(childNodes.item(j).getNodeName())
									&& appProps.getProperty("FAMILY_BASE_SFO_ID")
											.equalsIgnoreCase(childNodes.item(j).getTextContent())) {
								log.info("Found matching SFO_ID for family base 2 feature");
								for (int k = 0; childNodes != null && k < childNodes.getLength(); k++) {
									if (appProps.getProperty("FAMILY_BASE_ADD_STR")
											.equalsIgnoreCase(childNodes.item(k).getNodeName())) {
										// trigger SMS if SFO_ID is 78460 and
										// has effective date tag
										log.info("Found effective date tag for SFO_ID triggering SMS");
										String mtn = VisionAlertsProcessor.getNode(document,
												XPathFactory.newInstance().newXPath(),
												"//EDR_CPF//CHANGE_DATA_FEATURES//TR_KEY//TR_MDN//text()");

										return processor.triggerSMS(Long.parseLong(mtn),
												appProps.getProperty("FAMILY_BASE_SMS_REQ_TYPE"));
										
									}
								}
							}
							log.info("Node:" + childNodes.item(j).getNodeName() + " Value:"
									+ childNodes.item(j).getTextContent());
						}
					}
				}
				
			
			}			
			
		} catch (Exception e) {
			log.error("FamilyBaseAlert", "FamilyBaseSFOBean", "process", e.getMessage(), "FATAL", e);
		}

		return "P";
	}
}
