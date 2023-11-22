package com.verizon.onemsg.omppservice.mapper;

import java.util.Arrays;

import javax.naming.NamingEnumeration;
import javax.naming.NamingException;
import javax.naming.directory.Attributes;

import org.springframework.ldap.core.ContextMapper;
import org.springframework.ldap.core.DirContextAdapter;

import com.verizon.onemsg.omppservice.beans.Mdn;
import com.verizon.onemsg.omppservice.constants.LdapConstant;

public class MdnMapper implements ContextMapper<Mdn>{

	@Override
	public Mdn mapFromContext(Object ctx) throws NamingException {
		Mdn mdn = new Mdn();
		DirContextAdapter context = (DirContextAdapter) ctx;
		Attributes attributes = context.getAttributes();	

		NamingEnumeration<String> attrNames = attributes.getIDs();
		while (attrNames.hasMore()) {
			String attrName = attrNames.next();
			
			if (LdapConstant.LDAP_UID.equalsIgnoreCase(attrName)) {
				
				mdn.setUid(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_CUSTOMER.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwaccountnumber(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_BILLING_SYS.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwbillingsystem(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_CUST_TYPE.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwcustType(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_MDN_ROLE.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwroles(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_SECONDARY_EMAIL.equalsIgnoreCase(attrName)) {//MultiAttribute
				
				mdn.setVzwemail(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_PRIMARY_EMAIL.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwprimaryemail(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_ACCT_ACTIVATE_DT.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwaccountactivateddate(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_EMAIL_RT_FLAG.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwemailreturnedflag(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_MDN_MAKE.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwmake(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_MDN_MODEL.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwmodel(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_EMAIL_OPTINS.equalsIgnoreCase(attrName)) { //MultiAttribute
				
				mdn.setVzwemailnotifications(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_EMAIL_OPTOUTS.equalsIgnoreCase(attrName)) { //MultiAttribute
				
				mdn.setVzwemailoptouts(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_MDN_OPTINS.equalsIgnoreCase(attrName)) { //MultiAttribute
				
				mdn.setVzwmdnnotifications(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_MDN_OPTOUTS.equalsIgnoreCase(attrName)) { //MultiAttribute
				
				mdn.setVzwmdnoptouts(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_PUSH_OPTINS.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwPushNotifications(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_PUSH_OPTOUTS.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwPushOptouts(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_THRESHOLD_OPTINS.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwthresholdoptins(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_THRESHOLD_OPTOUTS.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwthresholdoptouts(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_THRESHOLD_ALT_EMAIL.equalsIgnoreCase(attrName)) { //MultiAttribute
				
				mdn.setVzwthresholdaltemail(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_THRESHOLD_ALT_MDN.equalsIgnoreCase(attrName)) { //MultiAttribute
				
				mdn.setVzwthresholdaltmdn(Arrays.asList(context.getStringAttributes(attrName)));
				
			} else if (LdapConstant.LDAP_THRESHOLD_VEHICLE.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwThresholdVehicles(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_WSMS_FLAG.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwWsmsFlag(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_GLOBAL_DATA_ALERT.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwGlobalDataAlert(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_GLOBAL_DATA_ALERT_VEHICLE.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwGlobalDataAlertVehicles(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_MDN_STATUS.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwmdnstatus(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_PRIMARY_EMAIL_UPDATE_DT.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwprimaryemailupdateddate(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_PRIMARY_EMAIL_VERIFY_DT.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwprimaryemailverifieddate(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_EMAIL_VALIDATE_DT.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwemailvalidateddate(context.getStringAttribute(attrName));
				
			}  else if (LdapConstant.LDAP_REGISTRATION_TYPE.equalsIgnoreCase(attrName)) {
				
				mdn.setVzwregistrationtype(context.getStringAttribute(attrName));
				
			} else if (LdapConstant.LDAP_PRECISION_DIRECT.equalsIgnoreCase(attrName)) {

				mdn.setVzwprecisiondirect(context.getStringAttribute(attrName));

			} else if (LdapConstant.LDAP_PRECISION_EMAIL.equalsIgnoreCase(attrName)) {

				mdn.setVzwprecisionemail(context.getStringAttribute(attrName));

			} else if (LdapConstant.LDAP_PRECISION_SMS.equalsIgnoreCase(attrName)) {

				mdn.setVzwprecisionsms(context.getStringAttribute(attrName));                                                                                                    
			}                                                                                                                                    

		}
		
		return mdn;
	}

}
