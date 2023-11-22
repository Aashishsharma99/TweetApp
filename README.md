package com.verizon.onemsg.omppservice.service;

import java.io.IOException;
import java.io.InputStream;
import java.io.StringWriter;
import java.sql.Clob;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import javax.xml.transform.TransformerException;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.w3c.dom.Document;

import com.verizon.onemsg.omppservice.beans.BOSAccountBean;
import com.verizon.onemsg.omppservice.beans.ClientActivity;
import com.verizon.onemsg.omppservice.beans.Mdn;
import com.verizon.onemsg.omppservice.beans.XSLBean;
import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.dao.CommonAuditDao;
import com.verizon.onemsg.omppservice.dao.OmppsDataManager;
import com.verizon.onemsg.omppservice.dao.OmppsLdapDAO;
import com.verizon.onemsg.omppservice.entity.XSLTransformerEntity;
import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.verizon.onemsg.omppservice.transfermation.XSLTransformer;
import com.verizon.onemsg.omppservice.util.CommonUtils;
import com.verizon.onemsg.omppservice.util.ReadWriteXml;
import com.verizon.onemsg.omppservice.util.SEPCommunication;
import com.verizon.onemsg.omppservice.util.XmlUtil;
import com.vzw.cc.valueobjects.AuditInfo;
import com.vzw.ecs.dm.message.ResponseDocument.Response;



@Service
public class ActivateProcessor {
	@Autowired
	AppProperties props ;
	
	@Autowired
	OmppsDataManager dataManager;
	
	@Autowired
	CommonAuditDao auditdao;
	
	@Autowired
	CommonUtils commonUtil;
	
	@Autowired
	ReadWriteXml readWriteXML;
	
	@Autowired
	OmppsLdapDAO omppsLdapDao;
	
	@Autowired
	VisionAlertsProcessor processor;
	
	@Autowired
	ChangeDataFeaturesProcessor changeDataFeaturesProcessor;
	@Autowired
	SEPCommunication sepCommunication;
	
	private Logger log = LogManager.getLogger(ActivateProcessor.class.getName());
	private static ActivateProcessor instance;
	
	public ActivateProcessor(){
		
	}
	
	
	public static void main(String[] args) {
		try{
		String message = "<?xml version=\"1.0\" encoding=\"UTF-8\"?><EDR_CPF>	<TR_COUNT>1</TR_COUNT>	<BILLING_SYSTEM_ID>7</BILLING_SYSTEM_ID>	<TRAN_ID>20140722153845189447</TRAN_ID>	<ACTIVATE>		<TR_KEY>			<TR_TYPE>ACT</TR_TYPE>			<TR_TIME>2014-07-22-15.38.45.185242</TR_TIME>			<TR_FULFILLMENT_TIME>2014-07-22-15.38.42.982195</TR_FULFILLMENT_TIME>			<TR_MDN>7322586954</TR_MDN>		</TR_KEY>		<MIN>7327358211</MIN>		<CUSTOMER_ID>287958786</CUSTOMER_ID>		<ACCOUNT_NUMBER>1</ACCOUNT_NUMBER>		<ZIP_CODE>07059-6747</ZIP_CODE>		<DEVICE_ID>A0000022767566</DEVICE_ID>		<DEVICE_ID_TYPE>ESN</DEVICE_ID_TYPE>		<DEVICE_MAKE>MOT</DEVICE_MAKE>		<DEVICE_MODEL>MOTOROLA DROID 2</DEVICE_MODEL>		<DEVICE_PROD_TYP>3GSmartphone</DEVICE_PROD_TYP>		<AREA>NE</AREA>		<BILL_DAY>22</BILL_DAY>		<FEATURE>			<FEATURE_CODE>6</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>8</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>9</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>10</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>16</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>23</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>29</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>61</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>62</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>399</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>1186</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>1189</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>1192</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>1464</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>1500</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>2146</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>2372</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>2430</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>2883</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>3992</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>4383</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<FEATURE>			<FEATURE_CODE>4387</FEATURE_CODE>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>		</FEATURE>		<SFO_ID>			<SFO_ID>68871</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>68870</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>68876</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>68873</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>68872</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>68874</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>48676</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>48526</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>68869</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>8953</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>71888</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>71886</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>71885</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>73520</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>73662</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>75104</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>75602</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>75668</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>76405</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>78869</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>80096</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<SFO_ID>			<SFO_ID>80101</SFO_ID>			<EFFECTIVE_DATE>2014-07-22-15.38.42.982195</EFFECTIVE_DATE>			<LIMIT_TYPE_CD>A</LIMIT_TYPE_CD>			<OUTLET_ID>0000013425</OUTLET_ID>			<SALES_REP_ID>ENC</SALES_REP_ID>		</SFO_ID>		<CUSTOMER_CLASS>DCB</CUSTOMER_CLASS>		<CUSTOMER_TYPE>PE</CUSTOMER_TYPE>		<PRICE_PLAN_ID>000089739</PRICE_PLAN_ID>		<PPLAN_EFFECTIVE_TIMESTAMP>2014-07-22-15.38.42.982195</PPLAN_EFFECTIVE_TIMESTAMP>		<PPLAN_SET_ID>0000</PPLAN_SET_ID>		<PRIM_SECO_CD>N</PRIM_SECO_CD>		<PRICE_PLAN_CONTRACT_TERM_ID>0020</PRICE_PLAN_CONTRACT_TERM_ID>		<MDN_EFFECTIVE_DATE>2014-07-22</MDN_EFFECTIVE_DATE>		<SW_ACTIVATE_DATE>2014-07-22-15.38.44.263911</SW_ACTIVATE_DATE>		<SW_DISC_TMSTAMP>9999-12-31-23.59.59.999999</SW_DISC_TMSTAMP>		<CUST_DVC_CRT_DATE>2014-07-22-15.38.42.982195</CUST_DVC_CRT_DATE>		<CUST_DVC_DEVICE_ID_TYP>ESN</CUST_DVC_DEVICE_ID_TYP>		<ACTV_REQ_DATE>2014-07-22</ACTV_REQ_DATE>		<LINE_OF_SERVICE_ID_PART1>1000991</LINE_OF_SERVICE_ID_PART1>		<LINE_OF_SERVICE_ID_PART2>287958786137849090</LINE_OF_SERVICE_ID_PART2>		<HANDICAP_IND>N</HANDICAP_IND>		<IN_SVC_DATE>2014-07-22</IN_SVC_DATE>		<PPLAN_EFF_DATE>2014-07-22</PPLAN_EFF_DATE>		<PPLAN_END_DATE>9999-12-31</PPLAN_END_DATE>	</ACTIVATE></EDR_CPF>";
		Document doc = XmlUtil.loadXMLFromString(message);
		new ActivateProcessor().processActivate(doc, AppConstants.Processes.ACTIVATION);
		}catch(Exception e){}
	}
	public String processActivate(Document dom, AppConstants.Processes service){
		log.debug("Inside ActivateProcessor :: processActivate()");
		XSLTransformer aXSLTransformer = XSLTransformer.getInstance();
		
		String status = "F";// F - Failure, P - Processed n Success
		try {
			String sourceXML = XmlUtil.getXml(dom);
			
			//OMR Auditing for Change
			ClientActivity clientActivity = readWriteXML.getClientDetails(dom, service);
			
			AuditInfo auditInfo = null;
			final String customerClass = clientActivity.getCustomerClass();
			log.info("Customer Class :  " + customerClass);
			
			if(String.valueOf(clientActivity.getMdn())!= null && String.valueOf(clientActivity.getMdn())!= "") {
			
			if(AppConstants.BOS_CUSTOMER_CLASS.equalsIgnoreCase(customerClass)) {
				auditInfo = auditdao.createAuditMap(sourceXML, AppConstants.VISION_CLIENTID, "BOS_ACTIVATION");
				}else {
					auditInfo = auditdao.createAuditMap(sourceXML, AppConstants.VISION_CLIENTID, "MDN_ACTIVATION");
				}
				auditdao.updateAuditMap(auditInfo, Long.toString(clientActivity.getMdn()), Long.toString(clientActivity.getCustomerId()), Long.toString(clientActivity.getAccountNumber()),"");			
				if(AppConstants.BOS_CUSTOMER_CLASS.equalsIgnoreCase(customerClass)) {				
					String acctNo = String.format("%05d", clientActivity.getAccountNumber());	
					final String custAcct = clientActivity.getCustomerId() + "-" + acctNo;
	            	log.debug("VZ Account Number: " + custAcct);	            
	            	BOSAccountBean bean = new BOSAccountBean(custAcct, AppConstants.VISION_CLIENTID, clientActivity.getEmailAddr(), clientActivity.getFirstName(), 
	            		clientActivity.getLastName());
	            	status = commonUtil.makeCallToBOSPreferenceCenter("ACTIVATE", bean, auditInfo);
	            	auditdao.submitAduitMessage(auditInfo, status);
	            	return status;
				}else {
					long mdn = clientActivity.getMdn();
					log.info("Requested MDN::"+mdn);
					String acctNo = String.format("%05d", clientActivity.getAccountNumber());
					// Search for the mdn in LDAP
					Mdn ldapMdn = omppsLdapDao.search(String.valueOf(mdn));
					if (ldapMdn == null) {
					// if mdn does not exist add new mdn
					log.info("VisionAlertsProcessor:processTransfer - mdn does not exist");
					status = processor.addMDNProfileToPrefCtr(String.valueOf(clientActivity.getCustomerId()), acctNo, String.valueOf(mdn), clientActivity.getBillingSystem());
				}
				
								
			
				 	//status = "P";
				 	clientActivity.setStatus(status);
				 	dataManager.updateActivationAudit(clientActivity);
				 	log.debug("Activate processing done with status :: "+status);
				 	//Do Auditing
				 	auditdao.submitAduitMessage(auditInfo, status);        			
				 	//Check if any SFO need communication
				 	changeDataFeaturesProcessor.processChangeDataFeatureSFO(dom, service);
			}}else {
				log.info("MDN Less Request . SKIPING!!! The CustomerID functionality..."+clientActivity.getCustomerId());
				log.info("This is a MDN-less request payload. Skipping. No furthur processing");
			}
		}
		catch(TransformerException te){
			log.debug("ActivateProcessor :: processActivate() : Exception while Transforming Vision XML to SEP V2.");
		}
		catch (Throwable e) {
			log.debug("ActivateProcessor :: processActivate() : Exception while Processing Vision Activate Message. Exception :: "+e.getMessage());
			e.printStackTrace();
		}
		return status;		
	}
	public String updateBPId(String resp, String templateName){
		if(resp != null && !resp.equals(""))
		{
				Pattern pattern = Pattern.compile(props.getProperty("BP_ID_IDENTIFIER")); 
				Matcher matcher = pattern.matcher(resp);
				while (matcher.find()) {
					resp = matcher.replaceFirst(templateName);
				}
				log.debug("Final SEPV2 XML : "+ resp);
			
		}
		return resp;
	}
}
