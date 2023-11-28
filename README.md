package com.verizon.onemsg.omppservice.service;

import java.io.ByteArrayInputStream;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.xpath.XPath;
import javax.xml.xpath.XPathConstants;
import javax.xml.xpath.XPathExpression;
import javax.xml.xpath.XPathFactory;

import org.apache.commons.lang3.StringUtils;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.apache.xmlbeans.XmlOptions;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.w3c.dom.Document;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import com.verizon.onemsg.omppservice.beans.Mdn;
import com.verizon.onemsg.omppservice.constants.AppConstants;
import com.verizon.onemsg.omppservice.dao.OmppsLdapDAO;
import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.verizon.onemsg.omppservice.util.CommonUtils;
import com.verizon.onemsg.omppservice.util.HttpCall;
import com.verizon.onemsg.omppservice.util.mail.MailBoxConnector;
import com.vzw.cc.cpc.xmlbeans.request.profileUpdate.Account;
import com.vzw.cc.cpc.xmlbeans.request.profileUpdate.Mobile;
import com.vzw.cc.cpc.xmlbeans.request.profileUpdate.ProfileUpdateRequest;
import com.vzw.cc.cpc.xmlbeans.request.profileUpdate.ProfileUpdateRequestDocument;
import com.vzw.cc.valueobjects.AuditInfo;

/**
 * @author c0peesr
 *
 *
 *         return code String value varies for different process A -- Process
 *         just Initiated D -- MDN Added to Disconnect H -- Folder copied
 *         successfully K -- MDN deleted from customer branch Y -- MDN add
 *         request sent successfully
 *
 */
@Component
public class VisionAlertsProcessor {

	@Value("${APP_ID}")
	private static String appId;

	@Value("${ENABLE_OU_DISCONNECT_FLAG}")
	boolean enableOuDisconnectFlag;

	private static Logger log = LogManager.getLogger(VisionAlertsProcessor.class.getName());

	/*
	 * @Autowired
	 * 
	 * @Qualifier("ldapTemplatePS") LdapTemplate ldapTemplatePS;
	 */

	@Autowired
	OmppsLdapDAO omppsLdapDao;

	@Autowired
	AppProperties prop;

	@Autowired
	CommonUtils commonUtils;

	@Autowired
	HttpCall httpCall;

	private static final String APPID = appId;

	public VisionAlertsProcessor() {
		// springLdapDao = (SpringLdapDAO)context.getBean("springLdapDao");
	}

	public String processTransfer(AuditInfo auditInfo, Node node) {

		String billSystem = auditInfo.getFlexField1(); // billSystemId.
		String processStatus = auditInfo.getStatus();
		// SpringLdapDAO springLdapDao = null;

		try {
			XPath xpath = XPathFactory.newInstance().newXPath();
			String customerId = CommonUtils.padWithZeros(getNode(node, xpath, "//TRANSFER//CUSTOMER_ID//text()"), 9);
			String accountNumber = CommonUtils.padWithZeros(getNode(node, xpath, "//TRANSFER//ACCOUNT_NUMBER//text()"),
					5);
			String orgAccountNumber = CommonUtils
					.padWithZeros(getNode(node, xpath, "//TRANSFER//ORIGINAL_ACCOUNT_NUMBER//text()"), 5);
			String mdn = getNode(node, xpath, "//TRANSFER//TR_KEY//TR_MDN//text()");

			if (mdn!= null && mdn !="") {
				// springLdapDao =ApplicationContextUtil.getBean("springLdapDao");
				String vzwCustAcctNo;

				// Audit Entries
				auditInfo.setNotification_address(mdn);
				auditInfo.setCust_id(customerId);
				auditInfo.setAcct_no(accountNumber);

				// Search for the mdn
				Mdn ldapMdn = omppsLdapDao.search(mdn);

				if (ldapMdn == null) {
					// if mdn does not exist add new mdn
					log.info("VisionAlertsProcessor:processTransfer - mdn does not exist");
					processStatus = addMDNProfileToPrefCtr(customerId, accountNumber, mdn, billSystem);
				} else {

					// if mdn exists and is associated with new account no further processing
					vzwCustAcctNo = ldapMdn.getVzwaccountnumber();
					if (vzwCustAcctNo.substring(vzwCustAcctNo.indexOf('-') + 1).equals(accountNumber)) {
						log.info(
								"VisionAlertsProcessor:processTransfer - mdn exists and is associated with new account no further processing");
						processStatus = AppConstants.MDN_ACTIVITY_FINAL_SUCCESS;
					} else {
						// if mdn exists and account number is equal to original account, delete old mdn
						// and new mdn
						log.info("VisionAlertsProcessor:processTransfer - mdn exist");
						processStatus = disconectMDN(customerId, orgAccountNumber, ldapMdn, processStatus,
								omppsLdapDao);
						if (processStatus != null
								&& AppConstants.MDN_DELETED_FROM_CUST_OU_SUCCESS.equals(processStatus))
							processStatus = addMDNProfileToPrefCtr(customerId, accountNumber, mdn, billSystem);
					}
				}
			} else {
				log.info("SKIPPING !!!MDN Less Payload. No furthur processing for this request with custId"+customerId);
				
			}
		} catch (Exception e) {
			log.error("CustActivityTransfer", "VisionAlertsProcessor", "processTransfer", e.getMessage(), "ERROR", e);

		}
		log.info("VisionAlertsProcessor:processTransfer - Status: " + processStatus);
		return processStatus;

	}

	public String addMDNProfileToPrefCtr(String customerId, String accountNumber, String mdn, String billSystem) {

		String status = null;
		String response = null;
		try {
			ProfileUpdateRequestDocument requestDoc = creatProfileRequest(customerId, accountNumber, mdn, billSystem);

			final String pcUpdateSvcUrl = prop.getProperty(AppConstants.PC_SERVICES_URL);
			final int timeOut = Integer.parseInt(prop.getProperty(AppConstants.PC_SERVICES_HTTP_TIMEOUT));

			XmlOptions opts = new XmlOptions();
			opts.setSavePrettyPrint();
			opts.setSavePrettyPrintIndent(4);
			opts.setUseDefaultNamespace();

			log.info("addMDNProfileToPrefCtr :: requestDoc : " + requestDoc);

			final String requestXML = requestDoc.xmlText(opts);

			if (pcUpdateSvcUrl != null && !pcUpdateSvcUrl.isEmpty()) {
				String contentType = "application/xml";
				log.debug("PCUpdate request: " + requestXML);
				response = httpCall.makeHttpPostRequest(pcUpdateSvcUrl, requestXML, timeOut, contentType);
				log.debug("PCUpdate response: " + response);
			}

			log.info("addMDNProfileToPrefCtr :: responseString : " + response);

			if (response != null && response.contains("000")) {

				status = AppConstants.ADD_MDN_PROFILE_TO_PC_SUCCESS;
			} else {

				status = AppConstants.PROCESS_INITIATED_OR_FAILED;
			}
		} catch (Exception ex) {

			log.error(prop.getProperty(AppConstants.APP_ID), "ProfileUpdateRequest", "VisionAlertsProcessor",
					"addMDNProfileToPrefCtr", ex.getMessage(), "ERROR", ex);
			status = AppConstants.PROCESS_INITIATED_OR_FAILED;

		} finally {
			log.info("addMDNProfileToPrefCtr END PROCESS->:status:" + status);
		}

		return status;
	}

	public String processChange(AuditInfo auditInfo, Document doc) throws Exception {
		// springLdapDao = ApplicationContextUtil.getBean("springLdapDao");
		XPath xpath = XPathFactory.newInstance().newXPath();
		String customerId = getNode(doc, xpath, AppConstants.MDN_CHG_CUSTID_XPATH);
		customerId = CommonUtils.padWithZeros(customerId, 9);
		String accountNumber = getNode(doc, xpath, AppConstants.MDN_CHG_ACCTNO_XPATH);
		accountNumber = CommonUtils.padWithZeros(accountNumber, 5);

		int billingSystemId = Integer.parseInt(getNode(doc, xpath, AppConstants.MDN_CHG_BILL_SYSID_XPATH));
		String billSystem = getBillSystem(billingSystemId);

		String newMdn = getNode(doc, xpath, AppConstants.MDN_CHG_NEW_MDN_XPATH);
		String oldMdn = getNode(doc, xpath, AppConstants.MDN_CHG_OLD_MDN_XPATH);

		auditInfo.setFlexField1(billSystem);

		String vzwCustAcctNo = null;
		boolean disconnect = false;
		String retryStatus = auditInfo.getStatus();

		// Search for the mdn
		Mdn ldapMdnOld = omppsLdapDao.search(oldMdn);
		Mdn ldapMdnNew = omppsLdapDao.search(newMdn);
		// Audit Entries
		auditInfo.setNotification_address(newMdn);
		auditInfo.setCust_id(customerId);
		auditInfo.setAcct_no(accountNumber);

		if (ldapMdnOld != null) {
			// if Old mdn exists and is associated with new account do further processing
			vzwCustAcctNo = ldapMdnOld.getVzwaccountnumber();
			if ((customerId + "-" + accountNumber).equals(vzwCustAcctNo)) {
				log.info(
						"VisionAlertsProcessor:processChange - Old mdn exists and is associated with new account-- Set disconnect=true");
				disconnect = true;

			} else {
				// if mdn exists and account number is not equal to original account, Leave it
				log.info("VisionAlertsProcessor:processChange - Old mdn and Account does not macth");
			}
		}

		if (ldapMdnNew == null) {
			// if new mdn does not exist then add new mdn
			log.info("VisionAlertsProcessor:processChange - New Mdn does not exist");
			retryStatus = addMDNProfileToPrefCtr(customerId, accountNumber, newMdn, billSystem);
			// Preferences of Old MDN has to be copied/reflected to New MDN
			ldapMdnNew = omppsLdapDao.search(newMdn);
			if (disconnect && retryStatus != null && AppConstants.ADD_MDN_PROFILE_TO_PC_SUCCESS.equals(retryStatus)
					&& ldapMdnOld != null) {
				// to make sure we don't update the mail,mailHost,mailMessageStore
				ldapMdnNew = transferOldPreferences(ldapMdnOld, ldapMdnNew);
				omppsLdapDao.updateMdn(ldapMdnNew); // Update the preferences.
			}
			if (disconnect && AppConstants.ADD_MDN_PROFILE_TO_PC_SUCCESS.equals(retryStatus))
				retryStatus = disconectMDN(customerId, accountNumber, ldapMdnOld,
						AppConstants.PROCESS_INITIATED_OR_FAILED, omppsLdapDao);

			if (retryStatus != null && AppConstants.MDN_DELETED_FROM_CUST_OU_SUCCESS.equals(retryStatus)) {
				log.info(
						"VisionAlertsProcessor:processChange - New Mdn Profile created and OLd MDN Disconnected :retryStatus:"
								+ retryStatus);
				return AppConstants.MDN_ACTIVITY_FINAL_SUCCESS;
			} else {
				log.info(
						"VisionAlertsProcessor:processChange - New Mdn Profile created and OLd MDN Disconnect Failed ::retryStatus:"
								+ retryStatus);
				// re-process the XML put back the message into the Queue
				return retryStatus;
			}

		} else {
			// if New mdn exists
			vzwCustAcctNo = ldapMdnNew.getVzwaccountnumber();
			log.info("CustId-acct: " + customerId + "-" + accountNumber);
			log.info("NewLdapAcctNo: " + vzwCustAcctNo);
			if ((customerId + "-" + accountNumber).equals(vzwCustAcctNo)) {

				// if New mdn Acct No and ldap account number MATCH
				log.info(
						"VisionAlertsProcessor:processChange - New mdn exists and is associated with new account no further processing");

				return AppConstants.MDN_ACTIVITY_FINAL_SUCCESS;
			} else {
				// if New mdn Acct No exists and ldap account number do NOT MATCH
				log.info("VisionAlertsProcessor:processChange - New mdn Account and ldap Account does not macth");
				// Disconnect new MDN
				retryStatus = disconectMDN(customerId, accountNumber, ldapMdnNew,
						AppConstants.PROCESS_INITIATED_OR_FAILED, omppsLdapDao);

				// Create new Profile for New MDN
				if (retryStatus != null && AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS.equals(retryStatus)) {
					retryStatus = addMDNProfileToPrefCtr(customerId, accountNumber, newMdn, billSystem);
					// return retryStatus;
				} else {
					log.info("VisionAlertsProcessor:processChange - New MDN Disconnect Failed :" + disconnect);
					// reprocess the XML
//					return false;
				}

				// No Need of transfering the old Preferences
				if (disconnect && retryStatus != null && AppConstants.ADD_MDN_PROFILE_TO_PC_SUCCESS.equals(retryStatus)
						&& ldapMdnOld != null) {
					ldapMdnNew = omppsLdapDao.search(newMdn);
					ldapMdnNew = transferOldPreferences(ldapMdnOld, ldapMdnNew);
					omppsLdapDao.updateMdn(ldapMdnNew);

					retryStatus = disconectMDN(customerId, accountNumber, ldapMdnOld,
							AppConstants.PROCESS_INITIATED_OR_FAILED, omppsLdapDao);

					if (retryStatus != null && AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS.equals(retryStatus)) {
						log.info(
								"VisionAlertsProcessor:processChange - New Mdn Profile created and OLd MDN Disconnected :"
										+ disconnect);
						return AppConstants.MDN_ACTIVITY_FINAL_SUCCESS;
					} else {
						log.info(
								"VisionAlertsProcessor:processChange - New Mdn Profile created and OLd MDN Disconnect Failed :"
										+ disconnect);
						// reprocess the XML
						return retryStatus;
					}
				} else if (retryStatus == null || !AppConstants.ADD_MDN_PROFILE_TO_PC_SUCCESS.equals(retryStatus)) {
					log.info("VisionAlertsProcessor:processChange - New MDN Disconnect Failed :" + disconnect
							+ "::retryStatus:" + retryStatus);
					// reprocess the XML
					return retryStatus;
				}
				log.info("VisionAlertsProcessor:processChange - New Mdn Profile created and OLd MDN Disconnected :"
						+ disconnect + "::retryStatus:" + retryStatus);

				return retryStatus;
			}
		}

	}

	public String disconectMDN(String vzwAccountNumber, Mdn mdn, String accountMailStore, String accountMailHost,
			String processStatus, OmppsLdapDAO omppsLdapDao) throws Exception {
		boolean result = false;
		String status = null;
		if (enableOuDisconnectFlag) {
			if (processStatus.charAt(0) < AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS.toCharArray()[0]) {
				status = doDisconnect(vzwAccountNumber, accountMailHost, accountMailStore, omppsLdapDao);
			}
			if (status != null && AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS.equals(status)) {
				if (commonUtils.connectToSunMsgServer(accountMailHost)) {
					MailBoxConnector eConn = new MailBoxConnector();
					result = eConn.getFolderstoMove(mdn, vzwAccountNumber, accountMailHost, accountMailStore);
				} else {
					log.info("Skip to Connect Sun Mail Servers - accountMailHost = " + accountMailHost);
					result = true;
				}
				if (result) {
					status = AppConstants.MDN_FOLDER_COPIED_SUCCESS;
				}
			}
			if (status != null && (AppConstants.MDN_FOLDER_COPIED_SUCCESS.equals(status)
					|| AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS.equals(status))) { // added as part of OMPPS
																						// Migration.
				result = omppsLdapDao.deleteMdn(mdn.getUid());
				log.info("MDNCleanupRequest:MDNCleanupProcessing- delete mdn is successful");
				if (result) {
					status = AppConstants.MDN_DELETED_FROM_CUST_OU_SUCCESS;
				}
			}
		} else {
			log.info("Skipping!!!loggic for OU-Disconnect and Proceed further to delete mdn in reassign prop***" + mdn);
			result = omppsLdapDao.deleteMdn(mdn.getUid());
			log.info("MDNCleanupRequest:MDNCleanupProcessing- delete mdn is successful");
			if (result) {
				status = AppConstants.MDN_DELETED_FROM_CUST_OU_SUCCESS;
			}
		}
		log.info("MDNCleanupRequest:disconectMDN()- delete mdn Status:" + status);
		return status;
	}

	public String disconectMDN(String customerId, String accountNumber, Mdn mdn, String processStatus,
			OmppsLdapDAO omppsDao) throws Exception {
		String accountMailStore = "mdn".concat(customerId.substring(customerId.length() - 1));
		String accountMailHost = buildMailHost(customerId).toString();
		String vzwAccountNo = customerId + "-" + accountNumber;

		return disconectMDN(vzwAccountNo, mdn, accountMailStore, accountMailHost, processStatus, omppsDao);
	}

	public String disconectMDN(String vzwAccountNumber, Mdn mdn, String processStatus, OmppsLdapDAO omppsDao)
			throws Exception {
		String customerId = vzwAccountNumber.substring(0, vzwAccountNumber.lastIndexOf('-'));
		String accountMailStore = "mdn".concat(customerId.substring(customerId.length() - 1));
		String accountMailHost = buildMailHost(customerId).toString();

		return disconectMDN(vzwAccountNumber, mdn, accountMailStore, accountMailHost, processStatus, omppsDao);
	}

	public String doDisconnect(String vzwAcctNo, String custMailHost, String custMailStore, OmppsLdapDAO omppsDao) {
		log.info("VisionAlertsProcessor: doDisconnect: getFolderstoMove");
		String returnStr = AppConstants.PROCESS_INITIATED_OR_FAILED;
		if (custMailHost == null) {
			log.info("VisionAlertsProcessor: X Ldap entry does not exist for vzwAcctNo=" + vzwAcctNo
					+ " folde not rcreated");
			returnStr = AppConstants.MDN_NOT_FOUND_IN_LDAP;

		} else {

			try {

				log.info(
						"VisionAlertsProcessor: .Finding Account entity entry if it already exist in Disconnects OU for vzwAcctNo#: "
								+ vzwAcctNo);
				Mdn mdn = omppsDao.searchDisconnect(vzwAcctNo);

				if (mdn == null) {

					log.info("VisionAlertsProcessor: adding Account entity entry in Disconnects OU for accoutn#: "
							+ vzwAcctNo);
					omppsDao.addDisconnect(vzwAcctNo, custMailHost, custMailStore);
					log.info("wait for 2 seconds after Account Entity is created for: " + vzwAcctNo + " " + custMailHost
							+ " " + custMailStore);
					returnStr = AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS;
				} else {
					custMailHost = mdn.getMailhost();
					custMailStore = mdn.getMailmessagestore();
					log.info("VisionAlertsProcessor: Account entity entry in Disconnects OU exist for accoutn#: "
							+ vzwAcctNo);
					returnStr = AppConstants.MDN_ADDED_TO_OU_DISCNT_SUCCESS;
				}

			} catch (Throwable e) {

				log.error(APPID, "VisionAlertsProcessor", "doDisconnect()", e.toString(), "FATAL", e, null);
				log.info("VisionAlertsProcessor:F Exception adding Account entity for " + vzwAcctNo + " " + e
						+ " folder not  created");
			}
		}
		return returnStr;
	}

	private ProfileUpdateRequestDocument creatProfileRequest(String customerId, String accountNumber, String mdn,
			String billSystem) {
		ProfileUpdateRequestDocument reqDoc = ProfileUpdateRequestDocument.Factory.newInstance();
		ProfileUpdateRequest req = reqDoc.addNewProfileUpdateRequest();
		req.setClientId(prop.getProperty(AppConstants.APP_ID));

		Account acct = req.addNewAccount();
		acct.setAccountNumber(accountNumber);
		acct.setBillingSystem(billSystem);
		acct.setCustomerId(customerId);
		acct.setRegistrationFlag("N");
		acct.setStatus("A");

		Mobile mobile = acct.addNewMobile();
		mobile.setMDN(mdn);
		mobile.setPrimary("N");
		mobile.setMobileRegistrationFlag("Y");
		return reqDoc;
	}

	public String getBillSystem(int billingSystemId) {

		switch (billingSystemId) {
		case 1:
			return "VISION_EAST";
		case 2:
			return "VISION_WEST";
		case 7:
			return "VISION_NORTH";
		case 8:
			return "VISION_B2B";
		default:
			return null;
		}
	}

	/**
	 * building document from the xmlString
	 * 
	 * @param xml
	 * @return
	 * @throws Exception
	 */

	public static Document loadXMLFromString(String xmlString) throws Exception {
		DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
		factory.setExpandEntityReferences(false);
		factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
		factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
		factory.setFeature("http://javax.xml.XMLConstants/feature/secure-processing", true);
		DocumentBuilder builder = factory.newDocumentBuilder();
		Document document = builder.parse(new ByteArrayInputStream(xmlString.getBytes("UTF-8")));
		return document;
	}

	public static String getNode(Document doc, XPath xpath, String xpathStr) throws Exception {
		String nodeValue = null;
		XPathExpression expr = xpath.compile(xpathStr);
		Object result = expr.evaluate(doc, XPathConstants.NODESET);
		NodeList nodes = (NodeList) result;

		for (int i = 0; i < nodes.getLength(); i++) {
			nodeValue = (String) nodes.item(i).getNodeValue();
			log.info("nodeValue:" + nodeValue);
		}
		return nodeValue;
	}

	static Mdn transferOldPreferences(Mdn ldapMdnOld, Mdn ldapMdnNew) {

		if (ldapMdnOld.getVzwemailoptouts() != null) {
			ldapMdnNew.setVzwemailoptouts(ldapMdnOld.getVzwemailoptouts());
		}

		if (StringUtils.isNotBlank(ldapMdnOld.getVzwaccountactivateddate()) && StringUtils.isNotEmpty(ldapMdnOld.getVzwaccountactivateddate())) {
			ldapMdnNew.setVzwaccountactivateddate(ldapMdnOld.getVzwaccountactivateddate());
		}
		if (StringUtils.isNotBlank(ldapMdnOld.getVzwprimaryemailupdateddate()) && StringUtils.isNotEmpty(ldapMdnOld.getVzwprimaryemailupdateddate())) {
			ldapMdnNew.setVzwprimaryemailupdateddate(ldapMdnOld.getVzwprimaryemailupdateddate());
		}
		if (StringUtils.isNotBlank(ldapMdnOld.getVzwprimaryemailverifieddate()) && StringUtils.isNotEmpty(ldapMdnOld.getVzwprimaryemailverifieddate())) {
			ldapMdnNew.setVzwprimaryemailverifieddate(ldapMdnOld.getVzwprimaryemailverifieddate());
		}

		if (ldapMdnOld.getVzwamemailoptouts() != null)
			ldapMdnNew.setVzwamemailoptouts(ldapMdnOld.getVzwamemailoptouts());

		if (ldapMdnOld.getVzwemail() != null)
			ldapMdnNew.setVzwemail(ldapMdnOld.getVzwemail());

		if (ldapMdnOld.getVzwemailnotifications() != null) {
			log.info("ldapMdnOld.getVzwemailnotifications():" + ldapMdnOld.getVzwemailnotifications());
			ldapMdnNew.setVzwemailnotifications(ldapMdnOld.getVzwemailnotifications());
		}

		if (ldapMdnOld.getVzwthresholdaltemail() != null)
			ldapMdnNew.setVzwthresholdaltemail(ldapMdnOld.getVzwthresholdaltemail());

		if (ldapMdnOld.getVzwmdnnotifications() != null)
			ldapMdnNew.setVzwmdnnotifications(ldapMdnOld.getVzwmdnnotifications());

		if (ldapMdnOld.getVzwmdnoptouts() != null)
			ldapMdnNew.setVzwmdnoptouts(ldapMdnOld.getVzwmdnoptouts());

		if (ldapMdnOld.getVzwmdnstatus() != null && !ldapMdnOld.getVzwmdnstatus().isEmpty()) {
			if (!ldapMdnOld.getVzwmdnstatus().contains("R")) {
				ldapMdnNew.setVzwmdnstatus(ldapMdnOld.getVzwmdnstatus());
			}
		}
		if (ldapMdnOld.getVzwprimaryemail() != null)
			ldapMdnNew.setVzwprimaryemail(ldapMdnOld.getVzwprimaryemail());

		if (ldapMdnOld.getVzwregistrationtype() != null)
			ldapMdnNew.setVzwregistrationtype(ldapMdnOld.getVzwregistrationtype());

		if (ldapMdnOld.getVzwroles() != null)
			ldapMdnNew.setVzwroles(ldapMdnOld.getVzwroles());

		if (ldapMdnOld.getVzwthresholdaltmdn() != null)
			ldapMdnNew.setVzwthresholdaltmdn(ldapMdnOld.getVzwthresholdaltmdn());

		if (ldapMdnOld.getVzwthresholdoptins() != null)
			ldapMdnNew.setVzwthresholdoptins(ldapMdnOld.getVzwthresholdoptins());

		if (ldapMdnOld.getVzwthresholdoptouts() != null)
			ldapMdnNew.setVzwthresholdoptouts(ldapMdnOld.getVzwthresholdoptouts());

		if (ldapMdnOld.getVzwprecisiondirect() != null)
			ldapMdnNew.setVzwprecisiondirect(ldapMdnOld.getVzwprecisiondirect());

		if (ldapMdnOld.getVzwprecisionemail() != null)
			ldapMdnNew.setVzwprecisionemail(ldapMdnOld.getVzwprecisionemail());

		if (ldapMdnOld.getVzwprecisionsms() != null)
			ldapMdnNew.setVzwprecisionsms(ldapMdnOld.getVzwprecisionsms());

		if (ldapMdnOld.getVzwWsmsFlag() != null)
			ldapMdnNew.setVzwWsmsFlag(ldapMdnOld.getVzwWsmsFlag());

		if (ldapMdnOld.getVzwGlobalDataAlert() != null)
			ldapMdnNew.setVzwGlobalDataAlert(ldapMdnOld.getVzwGlobalDataAlert());

		return ldapMdnNew;
	}

	public static String getNode(Node node, XPath xpath, String xpathStr) throws Exception {
		String nodeValue = null;
		XPathExpression expr = xpath.compile(xpathStr);
		Object result = expr.evaluate(node, XPathConstants.NODESET);
		NodeList nodes = (NodeList) result;

		for (int i = 0; i < nodes.getLength(); i++) {
			nodeValue = (String) nodes.item(i).getNodeValue();
			log.info("nodeValue:" + nodeValue);
		}
		return nodeValue;
	}

	public String processReassign(AuditInfo auditInfo, Node node) {

		String billSystem = auditInfo.getFlexField1(); // billingSystemId.
		String processStatus = auditInfo.getStatus();

		try {

			XPath xpath = XPathFactory.newInstance().newXPath();
			String customerId = CommonUtils.padWithZeros(getNode(node, xpath, "//REASSIGN//CUSTOMER_ID//text()"), 9);
			String accountNumber = CommonUtils.padWithZeros(getNode(node, xpath, "//REASSIGN//ACCOUNT_NUMBER//text()"),
					5);
			String mdn = getNode(node, xpath, "//REASSIGN//TR_KEY//TR_MDN//text()");
			String vzwCustAcctNo;

			if (mdn !=null  && mdn!="") {

				// Audit Entries
				auditInfo.setNotification_address(mdn);
				auditInfo.setCust_id(customerId);
				auditInfo.setAcct_no(accountNumber);

				// Search for the mdn
				Mdn ldapMdn = omppsLdapDao.search(mdn);

				if (ldapMdn == null) {
					// if mdn does not exist add new mdn
					log.info("VisionAlertsProcessor:processReassign - mdn does not exist");
					processStatus = addMDNProfileToPrefCtr(customerId, accountNumber, mdn, billSystem);
				} else {
					vzwCustAcctNo = ldapMdn.getVzwaccountnumber();
					// if mdn is already re-assigned do nothing
					if (!vzwCustAcctNo.equals(customerId + "-" + accountNumber)) {
						// if mdn exists and account number is equal to original account, delete old mdn
						// and new mdn
						log.info("VisionAlertsProcessor:processReassign - mdn exist");
						processStatus = disconectMDN(vzwCustAcctNo, ldapMdn, processStatus, omppsLdapDao);
						if (processStatus != null
								&& AppConstants.MDN_DELETED_FROM_CUST_OU_SUCCESS.equals(processStatus))
							processStatus = addMDNProfileToPrefCtr(customerId, accountNumber, mdn, billSystem);
					} else {
						log.info("VisionAlertsProcessor:processReassign - mdn exist with same account:" + mdn);
						processStatus = AppConstants.MDN_ACTIVITY_FINAL_SUCCESS;
					}
				}
			} else {
				log.info("Skipping !!! MDN less Payload. No furthur processing for this request");
				log.info("Customer Id ::"+auditInfo.getCust_id());
			}
		} catch (Throwable e) {
			log.error("CustActivityReassign", "VisionAlertsProcessor", "processReassign", e.getMessage(), "ERROR", e);
			// e.printStackTrace();
		}
		log.info("VisionAlertsProcessor:processReassign - Status: " + processStatus);
		return processStatus;
	}

	/**
	 * Determine target mailhost url based on next-to-last-digit of customer ID.
	 * 
	 * @param env    Environment set
	 * @param custId Customer ID to use for determination
	 * @return mailhost to use for account-definition
	 */
	public StringBuilder buildMailHost(String custId) {
		String ncustId = null;
		ncustId = 0 + custId;
		int custLen = ncustId.length();
		String no0custId = ncustId.substring(custLen - 9, custLen);
		StringBuilder accountmailhost = new StringBuilder();
		String env = prop.getProperty(AppConstants.ENV);

		// determine if 2nd-last digit is even or odd -- sets mailhost # (1 or
		// 2)/even or odd
		int i2EvenOdd = (no0custId.charAt(custLen - 2) - '0') % 2;
		// determine if 3rd-last digit is even or odd -- sets EAST/WEST /
		// even=EAST, odd=WEST
		int i3EvenOdd = (no0custId.charAt(custLen - 3) - '0') % 2;
		if (env.equalsIgnoreCase("dev")) {
			accountmailhost = new StringBuilder(prop.getProperty(AppConstants.PREPROD_MAILHOST_PREFIX));
			if (i2EvenOdd == 0) { // check if even or odd number
				accountmailhost.append("mcs2"); // even then even mailhost#
			} else {
				accountmailhost.append("mcs1"); // odd then odd mailhost#
			}
			accountmailhost.append(prop.getProperty(AppConstants.EXT));
		} else if (env.equalsIgnoreCase("prod")) {
			String suffix = "";
			if (i3EvenOdd == 0) { // if even set for EAST
				accountmailhost.append(prop.getProperty("PROD_EAST_MAILHOST_PREFIX"));
				suffix = prop.getProperty(AppConstants.EAST);
			} else { // if odd set for WEST
				accountmailhost.append(prop.getProperty("PROD_WEST_MAILHOST_PREFIX"));
				suffix = prop.getProperty(AppConstants.WEST);
			}
			accountmailhost.append("-");
			if (i2EvenOdd == 0) {
				accountmailhost.append("mcs2");
			} else {
				accountmailhost.append("mcs1");
			}
			accountmailhost.append(suffix);
		} else {
			log.error("OMPPS", "buildMailHost()", "VisionAlertsProcessor", "buildMailHost()",
					"Unknown ENV property set:" + env, AppConstants.FATAL, null);
		}
		log.debug("VisionAlertsProcessor :: accountmailhost: " + accountmailhost);
		return accountmailhost;
	}

}
