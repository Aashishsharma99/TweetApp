package com.vzw.cc.valueobjects;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

import org.apache.xmlbeans.XmlObject;

/**
 * @author c0bha1m
 */
public class AuditInfo implements Serializable {
	public void addTrans(String transType, String className, String methodName, String req, String resp, String reqTS, Long respTime, String extSystem, String status) {
		TranLog tranLog = new TranLog();
		tranLog.put(TranLog.req_id,uniqueId);
		tranLog.put(TranLog.seq_no,new Long(++index));
		tranLog.put(TranLog.trns_type,transType);
		if (className!= null && className.length() > CLASS_NAME_LENGTH)
			tranLog.put(TranLog.class_name,className.substring(className.lastIndexOf('.')+1,className.length()));
		else
			tranLog.put(TranLog.class_name,className);
		tranLog.put(TranLog.method_name,methodName);
		if (req != null && req.length() > MAX_LENGTH)
			tranLog.put(TranLog.req_input,req.substring(0,MAX_LENGTH-1));
		else
			tranLog.put(TranLog.req_input,req);
		if (resp != null && resp.length() > MAX_LENGTH)
			tranLog.put(TranLog.resp_output,resp.substring(0,MAX_LENGTH-1));
		else
			tranLog.put(TranLog.resp_output,resp);
		tranLog.put(TranLog.req_ts,reqTS);
		tranLog.put(TranLog.response_time ,respTime);
		tranLog.put(TranLog.ext_system,extSystem);
		tranLog.put(TranLog.status,status);		
		addTrans(tranLog);
	}
	
	public void addTrans(TranLog transLog)
	{
		if (transLog != null) {
			if (tranLogs == null)
				tranLogs = new ArrayList<TranLog>();
			tranLogs.add(transLog);
		}
	}
