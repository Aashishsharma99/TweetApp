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
