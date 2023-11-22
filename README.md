package com.verizon.onemsg.omppservice.receiver;

import javax.annotation.PostConstruct;
import javax.jms.DeliveryMode;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;
import javax.xml.xpath.XPath;
import javax.xml.xpath.XPathConstants;
import javax.xml.xpath.XPathExpression;
import javax.xml.xpath.XPathFactory;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;
import org.w3c.dom.NodeList;

import com.ibm.mq.jms.MQQueue;
import com.ibm.msg.client.wmq.WMQConstants;
import com.verizon.onemsg.omppservice.beans.VisionBean;
import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.handler.FamilyBaseSFOHandler;
import com.verizon.onemsg.omppservice.handler.MDNChangeHandler;
import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.verizon.onemsg.omppservice.service.ChangeDataFeaturesProcessor;
import com.verizon.onemsg.omppservice.service.VisionAlertsProcessor;
import com.verizon.onemsg.omppservice.util.HttpCall;
import com.verizon.onemsg.omppservice.util.ReadWriteXml;

@Service
public class MDNChangeIBMreceiver implements MessageListener{
	private Logger log = LogManager.getLogger(MDNChangeReceiver.class.getName());
	private int maxRetryCount = 3;
	private String retryFlag = "N";
	
	@Value("${ompp.ibm.backout.queue.mdnchange:OMSG.CUSTACTIVITYCHANGEBKP.QA}")
	String changeBackoutQueue;
	
	@Autowired
	MDNChangeHandler mdnChangeHandler;

	@Autowired
	FamilyBaseSFOHandler familySfoHandler;
	
	@Autowired
	ChangeDataFeaturesProcessor changeDataFeaturesProcessor;

	@Autowired
	ReadWriteXml readWriteXml;
	
	@Autowired
	HttpCall httpcall;

	@Autowired
	AppProperties prop;

	@Autowired
	JmsTemplate jmsTemplate;

	@PostConstruct
	public void initialize() {
		
		maxRetryCount = Integer.parseInt(prop.getProperty(AppConstants.MDB_MAX_RETRY , "3"));
		retryFlag = prop.getProperty(AppConstants.MDB_RETRY , "N");
	}
	
	 public void onMessage(Message msg) {
			String processCode = null;
			TextMessage txtMsg = (TextMessage) msg;		
			int deliveryCount = 0;
			//skip the processing in case of deliveryCount more than configured maxRetryCount
			if ("Y".equals(retryFlag)) {
				try {
					deliveryCount = msg.getIntProperty("JMSXDeliveryCount");
					if (deliveryCount > maxRetryCount) {
						MQQueue queueDest = null;
						try {
							queueDest = new MQQueue(changeBackoutQueue);
							queueDest.setPutAsyncAllowed(WMQConstants.WMQ_PUT_ASYNC_ALLOWED_ENABLED);
							queueDest.setPersistence(DeliveryMode.NON_PERSISTENT);
						}catch(Exception ex){
							log.error("Unable to create queue destination: ", ex);
						}
						log.info("Message Moving to Change Backout Q after maxRetryCount = " + maxRetryCount);
						jmsTemplate.convertAndSend(queueDest, txtMsg.getText());
						log.info("Message Moved to Change Backout Q after maxRetryCount = " + maxRetryCount);
						return;
	
					}
				} catch (Exception e) {
					log.warn("Failed to Move message to Change BackoutQ", e);
				}
			}
			
			try {
				String strMsg = txtMsg.getText();
				log.debug("Request XML:" + strMsg);						
				strMsg = strMsg.replaceAll("[^\\x20-\\x7e]", "");
				Document doc = VisionAlertsProcessor.loadXMLFromString(strMsg);
				XPath xpath = XPathFactory.newInstance().newXPath();
	
				XPathExpression expr = xpath.compile(AppConstants.SMSSENT);
				NodeList nodes = (NodeList)expr.evaluate(doc, XPathConstants.NODESET);
				
				if (nodes == null || nodes.getLength() == 0) {
	
					expr = xpath.compile(AppConstants.SFOID);
					nodes = (NodeList)expr.evaluate(doc, XPathConstants.NODESET);
					
					if (nodes != null && nodes.getLength() > 0) {
						log.info("SFO ID found in the request, now processing it...");
						processCode = familySfoHandler.process(doc, processCode);
						
						//Identify SFO and do the Communication 					
						log.info("SFO_ID Tag Exist in Request XML");
						String status = changeDataFeaturesProcessor.processChangeDataFeatureSFO(doc, AppConstants.Processes.CHANGE);
						log.info("ChangeDataFeaturesProcessor completed with status :: " + status);			
						
					}
					log.info("Request in for the first time, marking it for SMS sent");
					//strMsg = txtMsg.getText();
					strMsg = strMsg.replace("</EDR_CPF>","<SMS_SENT>Y</SMS_SENT></EDR_CPF>");
					//txtMsg.clearBody();
					//txtMsg.setText(strMsg);
				}	
				expr = xpath.compile(AppConstants.NEW_MDN);
				nodes = (NodeList)expr.evaluate(doc, XPathConstants.NODESET);
				if (nodes != null && nodes.getLength() > 0) {
					processCode = mdnChangeHandler.process(doc, processCode);
					VisionBean vb = readWriteXml.readVisionXML(strMsg);
					//Set Client Id for Vision 
					vb.setClientId(AppConstants.VISION_CLIENTID);	
							
					//Write Message for Data Layer MDB
					String msgXML = ReadWriteXml.writeXML(vb);
					//Send Vision Message to DM (TDC system) to save in Database
					httpcall.sendXMLtoDM(msgXML);
				}
	
				else {
					processCode="X";
					// do nothing
					log.info("Not a valid NEW_MDN Change XML ");
				}
				
			} catch (Exception ex) {
	
				log.error("CustActivityChangeError", "VisionActivitiesMDB",
						"CustActivityChangeMDBBean", "onMessage", ex.getMessage(),
						"ERROR", ex);
	
	
	
			} finally {
				//log.info("Finally - processCode: " + processCode + " retryFlag : " + retryFlag);
				
				/*if ("Y".equals(retryFlag)) {
					try {					
						if (processCode != null && (!AppConstants.MDN_ACTIVITY_FINAL_SUCCESS.equals(processCode)
								&& !AppConstants.MDN_NOT_FOUND_IN_LDAP.equals(processCode))) {
	
							log.debug("deliveryCount =" + deliveryCount + " maxRetryCount =" + maxRetryCount + " jmsId="
									+ msg.getJMSMessageID());
	
							if (deliveryCount <= maxRetryCount) {
								log.info("Rollback jms msg : deliveryCount = " + deliveryCount + " :processCode = "
										+ processCode);
								mdc.setRollbackOnly();
							}
						}
					} catch (Exception e) {
						log.error("CustActivityChange", "CustActivityChangeMDBBean", "onMessage-finally", e.getMessage(),
								"FATAL", e);
					}
				}*/ 
			}
	
		}}
