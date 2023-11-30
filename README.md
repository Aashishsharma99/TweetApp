package com.verizon.onemsg.omppservice.util;

import java.io.IOException;
import java.io.InputStream;
import java.io.Reader;
import java.io.StringWriter;
import java.sql.Clob;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.List;

/*
 * Created on Aug 1, 2007
 *
 * TODO To change the template for this generated file go to
 * Window - Preferences - Java - Code Style - Code Templates
 */

import org.apache.commons.codec.binary.Base64;
import org.apache.commons.collections4.CollectionUtils;
import org.apache.commons.collections4.MapUtils;
import org.apache.commons.io.IOUtils;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.codehaus.jackson.map.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import com.verizon.onemsg.omppservice.beans.BOSAccountBean;
import com.verizon.onemsg.omppservice.beans.XSLBean;
import com.verizon.onemsg.omppservice.entity.XSLTransformerEntity;
import com.verizon.onemsg.omppservice.properties.AppProperties;
import com.vzw.cc.valueobjects.AuditInfo;

/**
 * @author beherp
 *
 */
@Component
public class CommonUtils {	
	
	@Value("${BOS_AUTH_USERNAME}")
	private String bosUserName;
	
	@Value("${BOS_AUTH_PASSWORD}")
	 String bosPassword;
	
	@Value("${BOS_PC_SERVICES_HTTP_TIMEOUT}")
	String bosBosPcHttpTimeout;
	
	@Value("${BOS_PC_SERVICE_ACTIVATE_ENDPOINT}")
	String bosPcServiceActivateEndpoint;
	
	@Value("${BOS_PC_SERVICE_DEACTIVATE_ENDPOINT}")
	String bosPcServiceDeactivateEndPoint;
	
	@Value("${APP_ID}")
	String appId;
	
	@Value("${ENV}")
	private static String environment;
	
	@Autowired
	AppProperties props;
	
	@Autowired
	HttpCall httpCall;
	
	private Logger log = LogManager.getLogger(CommonUtils.class.getName());
	
    public  String makeCallToBOSPreferenceCenter(final String activityType , BOSAccountBean bean , AuditInfo auditInfo) {
        String bosPcEndPoint = null;
        final String USERNAME = bosUserName;
        final String PASSWORD = bosPassword;
        final int timeOut = Integer.parseInt(bosBosPcHttpTimeout);
        String status = null;
        String queryData = null;
        ObjectMapper mapper = null;
        try {
            mapper = new ObjectMapper();         
            queryData = mapper.writeValueAsString(bean);    
            if ("ACTIVATE" .equals(activityType)) {            	
            	bosPcEndPoint = bosPcServiceActivateEndpoint;
            	
            } else if("DEACTIVATE" .equals(activityType)){            	
            	bosPcEndPoint = bosPcServiceDeactivateEndPoint;
            }
            
            final String USER_PASS = USERNAME + ":" + PASSWORD;
	    	String basicAuth = "Basic " + new String(new Base64().encode(USER_PASS.getBytes()));
	    	log.info("Calling BOS Preference Center - queryData : " + queryData);            
            String responseStr = httpCall.submitJSONToUrl(bosPcEndPoint, queryData, timeOut, basicAuth);
            log.info("Calling BOS Preference Center - response : " + responseStr);            
            auditInfo.setSynchResponse(responseStr);
            if(responseStr != null && responseStr.equals("200")) {
                status = "P";
            } else {
                status = "F";
            }
        } catch(Exception ex) {
        	log.error(appId, "makeCallToBOSPreferenceCenter", CommonUtils.class.getName(), "makeCallToBOSPreferenceCenter", ex.getMessage(), "ERROR", ex);
            status = "F";
        } finally {
        	log.info("makeCallToBOSPreferenceCenter END PROCESS->:status:" + status);
        	auditInfo.setStatus(status);
        }
        return status;
    }
    
    
	public boolean  connectToSunMsgServer(final String mailhost) {

		boolean connect = false;

		String env = environment;
		
		if ("prod".equalsIgnoreCase(env) && mailhost != null) {

			if (mailhost.contains(props.getProperty("PROD_EAST_MAILHOST_PREFIX"))
					&& "ON".equals(props.getProperty("CONNECT_TO_SUN_MESSAGE_SERVER_EAST", "ON"))) {

				connect = true;

			} else if (mailhost.contains(props.getProperty("PROD_WEST_MAILHOST_PREFIX"))
					&& "ON".equals(props.getProperty("CONNECT_TO_SUN_MESSAGE_SERVER_WEST", "ON"))) {

				connect = true;
			}
		}

		return connect;
	}
		
}
