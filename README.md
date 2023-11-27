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
