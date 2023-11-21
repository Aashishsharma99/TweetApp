package com.verizon.onemsg.omppservice.dao;

import java.io.ByteArrayOutputStream;
import java.io.ObjectOutputStream;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Date;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Component;
import org.springframework.util.SerializationUtils;

import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.vzw.cc.util.UniqueIdGenerator;
import com.vzw.cc.valueobjects.AuditInfo;

@Component
public class CommonAuditDao {
	
	private static Logger log = LogManager.getLogger(CommonAuditDao.class.getName());
	
	@Value("${APP_ID}")
	String appId;
	
	@Value("${PC_ADMIN_AUDIT_DATE_FORMAT}")
	String pcAdminDateFormat;
	
	@Value("${RMQ_AUDIT_EXCHANGE}")
	String auditExchange;
	
	@Value("${RMQ_OMPPS_PCADMIN_ROUTING_KEY}")
	String pcRoutingKey;
	
	RabbitTemplate pcauditRabbitTemplate;
	
	@Autowired
	AppProperties props ;
	
	@Autowired
	OmppsDataManager dataManager;
	
	public CommonAuditDao(@Lazy RabbitTemplate pcauditRabbitTemplate){
		this.pcauditRabbitTemplate = pcauditRabbitTemplate;
	}
	

	public AuditInfo createAuditMap(String reqXML, String clientId, String service){
		AuditInfo auditInfo = new AuditInfo(Long.valueOf(UniqueIdGenerator.getUniqId()));
		auditInfo.setReqInput(reqXML);
		auditInfo.setGeneralInfo(appId, service,  getAuditDate());
		auditInfo.setClient_id(clientId);
		auditInfo.setServer(System.getProperty(AppConstants.SERVERID));
		return auditInfo;
	}

	public void updateAuditMap(AuditInfo auditInfo , String mdn, String custId, String accNo, String sfoId){
		
		auditInfo.setCust_id(custId);
		auditInfo.setNotification_address(mdn);
		auditInfo.setAcct_no(accNo);
		auditInfo.setTrns_type(sfoId);			
	}
	public void updateAuditMap(AuditInfo auditInfo, String status){	
			
		auditInfo.setStatus(status);
		auditInfo.setResponse_TS(getAuditDate());
		
		try {
			SimpleDateFormat sdf = new SimpleDateFormat(
					props.getProperty((AppConstants.PC_ADMIN_AUDIT_DATE_FORMAT),"MMM-dd-yyyy HH:mm:ss"));
			auditInfo.setResponse_time(System.currentTimeMillis() - sdf.parse(auditInfo.getResponse_TS()).getTime());
		} catch (ParseException e) {
			log.warn("unable to parse response time for audit", e);
		}
		
	}
	
	public void submitAduitMessage(AuditInfo auditInfo){
		try {
			log.info("Sending to PC admin custid ::"+auditInfo.getCust_id() +"AccountNo :: "+ auditInfo.getAcct_no());
			log.info("Message send to ****** "+auditExchange+"***Routing Key****"+pcRoutingKey);
			byte [] auditData = SerializationUtils.serialize(auditInfo);
			log.info("Size of the code ::"+auditData.length);
			pcauditRabbitTemplate.convertAndSend(auditExchange, pcRoutingKey, auditData);
			
		} catch (Exception e) {
			log.error("Sending Auditing Message", "CommonAuditing", "submitAduitMessage", e.getMessage(), "Error", e);
		}
	}
	
	public  void submitAduitMessage(AuditInfo auditInfo, String status){
		try {
			log.info("Sending audit request to Audit Q:" + props.getProperty(AppConstants.AUDIT_Q_NAME));
			updateAuditMap(auditInfo, status);
			submitAduitMessage(auditInfo);
		} catch (Exception e) {
			log.error("Sending Auditing Message", "CommonAuditing", "submitAduitMessage", e.getMessage(), "Error", e);
		}		
		
	}
	
	
	public static String encodeObj(Object obj)  throws Exception{

		ByteArrayOutputStream bo = new ByteArrayOutputStream();
		ObjectOutputStream oo = new ObjectOutputStream(bo);
		oo.writeObject(obj);
		oo.flush();
		String encodedMsg = Base64.getEncoder().encodeToString(bo.toByteArray()); 
		return encodedMsg;
	}
	
	public String getAuditDate() {
		
		SimpleDateFormat sdf = new SimpleDateFormat(
				props.getProperty((AppConstants.PC_ADMIN_AUDIT_DATE_FORMAT),"MMM-dd-yyyy HH:mm:ss"));
		return sdf.format(new Date());
	}


	
}
