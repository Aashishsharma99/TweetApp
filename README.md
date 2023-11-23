/*
 * Created on Jun 12, 2006
 */
package com.vzw.cc.valueobjects;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

import org.apache.xmlbeans.XmlObject;

/**
 * @author c0bha1m
 */
public class AuditInfo implements Serializable {
	private static final long serialVersionUID = 4980137018656621678L;
	private XmlObject auditLog;
	private List<TranLog> tranLogs;
	private Long uniqueId;
	private String appl_id;
	private String service_name;	
	private String trns_type;
	private String notification_address;
	private String external_sys;
	private String cust_id;
	private String acct_no;
	private String reqInput = null;
	private String synchResponse = null;
	private String asynchResponse = null;
	private String request_TS;
	private String response_TS;
	private Long response_time;
	private String client_id;
	private String process_type;
	private String server;
	private String flexField1 = null;
	private String flexField2 = null;
	private String flexField3 = null;
	private String subTrnsType = null;
	private String msgType = null;
	
	private transient int index = 0;
	private static final int MAX_LENGTH=3000;
	private static final int CLASS_NAME_LENGTH=50;	
	
	private String entityType=null;
	private String operation=null;
	private String status=null;	
		
	public static final String entityTypeACC= "ACC";
	public static final String entityTypeMDN="MDN";
	public static final String operationAdd = "ADD";
	public static final String operationUpdate = "UPD";
	
	private List<XmlObject> trackLdapOps = null;
	
	public AuditInfo(Long uniqueId) {
		this.uniqueId = uniqueId;
	}
		
	public void setGeneralInfo(String appl_id,String service_name, String request_TS) {
		this.appl_id = appl_id;
		this.service_name = service_name;
		this.request_TS = request_TS;
	}
	
	public void setGeneralInfo(String appl_id,String service_name) {
		this.appl_id = appl_id;
		this.service_name = service_name;
	}
	
	public void setBusinessInfo(XmlObject auditLog)
	{
		this.auditLog = auditLog;
	}
	
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
	
	public XmlObject getBusinessInfo()
	{
		return auditLog;
	}
	
	public List<TranLog> getTranLogs()
	{
		return tranLogs;
	}	
	
	/**
	 * @return Returns the appl_id.
	 */
	public String getAppl_id() {
		return appl_id;
	}
	/**
	 * @return Returns the service_name.
	 */
	public String getService_name() {
		return service_name;
	}
	/**
	 * @return Returns the response_time.
	 */
	public Long getResponse_time() {
		return response_time;
	}
	/**
	 * @param response_time The response_time to set.
	 */
	public void setResponse_time(Long response_time) {
		this.response_time = response_time;
	}
	/**
	 * @return Returns the response_TS.
	 */
	public String getResponse_TS() {
		return response_TS;
	}
	/**
	 * @param response_TS The response_TS to set.
	 */
	public void setResponse_TS(String response_TS) {
		this.response_TS = response_TS;
	}
	/**
	 * @return Returns the request_TS.
	 */
	public String getRequest_TS() {
		return request_TS;
	}
	/**
	 * @param request_TS The request_TS to set.
	 */
	public void setRequest_TS(String request_TS) {
		this.request_TS = request_TS;
	}
	/**
	 * @return Returns the client_id.
	 */
	public String getClient_id() {
		return client_id;
	}
	/**
	 * @param client_id The client_id to set.
	 */
	public void setClient_id(String client_id) {
		this.client_id = client_id;
	}
	/**
	 * @return Returns the process_type.
	 */
	public String getProcess_type() {
		return process_type;
	}
	/**
	 * @param process_type The process_type to set.
	 */
	public void setProcess_type(String process_type) {
		this.process_type = process_type;
	}
	/**
	 * @return Returns the uniqueId.
	 */
	public Long getUniqueId() {
		return uniqueId;
	}
	/**
	 * @return Returns the synchResponse.
	 */
	public String getSynchResponse() {
		return synchResponse;
	}
	/**
	 * @param synchResponse The synchResponse to set.
	 */
	public void setSynchResponse(String synchResponse) {
		this.synchResponse = synchResponse;
	}
	/**
	 * @return Returns the server.
	 */
	public String getServer() {
		return server;
	}
	/**
	 * @param server The server to set.
	 */
	public void setServer(String server) {
		this.server = server;
	}
	
	/**
	 * @return Returns the entityType.
	 */
	public String getEntityType() {
		return entityType;
	}
	/**
	 * @param entityType The entityType to set.
	 */
	public void setEntityType(String entityType) {
		this.entityType = entityType;
	}
	/**
	 * @return Returns the operation.
	 */
	public String getOperation() {
		return operation;
	}
	/**
	 * @param operation The operation to set.
	 */
	public void setOperation(String operation) {
		this.operation = operation;
	}
	/**
	 * @return Returns the status.
	 */
	public String getStatus() {
		return status;
	}
	/**
	 * @param status The status to set.
	 */
	public void setStatus(String status) {
		this.status = status;
	}
	/**
	 * @return Returns the reqInput.
	 */
	public String getReqInput() {
		return reqInput;
	}
	/**
	 * @param reqInput The reqInput to set.
	 */
	public void setReqInput(String reqInput) {
		this.reqInput = reqInput;
	}
	/**
	 * @return Returns the flexField1.
	 */
	public String getFlexField1() {
		return flexField1;
	}
	/**
	 * @param flexField1 The flexField1 to set.
	 */
	public void setFlexField1(String flexField1) {
		this.flexField1 = flexField1;
	}
	/**
	 * @return Returns the flexField2.
	 */
	public String getFlexField2() {
		return flexField2;
	}
	/**
	 * @param flexField2 The flexField2 to set.
	 */
	public void setFlexField2(String flexField2) {
		this.flexField2 = flexField2;
	}
	/**
	 * @return Returns the flexField3.
	 */
	public String getFlexField3() {
		return flexField3;
	}
	/**
	 * @param flexField3 The flexField3 to set.
	 */
	public void setFlexField3(String flexField3) {
		this.flexField3 = flexField3;
	}
	/**
	 * @return Returns the asynchResponse.
	 */
	public String getAsynchResponse() {
		return asynchResponse;
	}
	/**
	 * @param asynchResponse The asynchResponse to set.
	 */
	public void setAsynchResponse(String asynchResponse) {
		this.asynchResponse = asynchResponse;
	}
	/**
	 * @return Returns the external_sys.
	 */
	public String getExternal_sys() {
		return external_sys;
	}
	/**
	 * @param external_sys The external_sys to set.
	 */
	public void setExternal_sys(String external_sys) {
		this.external_sys = external_sys;
	}
	/**
	 * @return Returns the msgType.
	 */
	public String getMsgType() {
		return msgType;
	}
	/**
	 * @param msgType The msgType to set.
	 */
	public void setMsgType(String msgType) {
		this.msgType = msgType;
	}
	/**
	 * @return Returns the notification_address.
	 */
	public String getNotification_address() {
		return notification_address;
	}
	/**
	 * @param notification_address The notification_address to set.
	 */
	public void setNotification_address(String notification_address) {
		this.notification_address = notification_address;
	}
	/**
	 * @return Returns the subTrnsType.
	 */
	public String getSubTrnsType() {
		return subTrnsType;
	}
	/**
	 * @param subTrnsType The subTrnsType to set.
	 */
	public void setSubTrnsType(String subTrnsType) {
		this.subTrnsType = subTrnsType;
	}
	/**
	 * @return Returns the trns_type.
	 */
	public String getTrns_type() {
		return trns_type;
	}
	/**
	 * @param trns_type The trns_type to set.
	 */
	public void setTrns_type(String trns_type) {
		this.trns_type = trns_type;
	}
	/**
	 * @return Returns the acct_no.
	 */
	public String getAcct_no() {
		return acct_no;
	}
	/**
	 * @param acct_no The acct_no to set.
	 */
	public void setAcct_no(String acct_no) {
		this.acct_no = acct_no;
	}
	/**
	 * @return Returns the cust_id.
	 */
	public String getCust_id() {
		return cust_id;
	}
	/**
	 * @param cust_id The cust_id to set.
	 */
	public void setCust_id(String cust_id) {
		this.cust_id = cust_id;
	}

	public List<XmlObject> getTrackLdapOps() {
		return trackLdapOps;
	}

	public void setTrackLdapOps(List<XmlObject> trackLdapOps) {
		this.trackLdapOps = trackLdapOps;
	}

	
}


package com.vzw.cc.valueobjects;
import java.io.File;
import java.util.*;
import java.util.logging.Logger;

public class GeneralUtilities {
	public static Logger logger = Logger.getLogger(GeneralUtilities.class.getName());

	public static final String HYPEN = "-";
	public static final String HYPEN_REPLACER="__";
	public static final String PH_Q = "?";
	public static final String PH_C = ",";
	public static final String PH_E = "=";
	public static final String PH_U = "_";
	public static final String PH_P = "%";
	public static final String PH_ULHY = "_-";
	public static final String NS_PREFIX_XSD = "xsd:";
	public static final String NOT_ALLOWED_IN_JAVA_PACKAGE=" -.";
	public static final String EXTENTION_JAVA=".java";
	public static final String REAP_PACKAGE="com.reap.";

    public static String capFirst(String in)
    {
    	String temp = null;

        if (in == null) return null;
        temp=in.substring(0, 1).toUpperCase() + in.substring(1, in.length());
		temp = hypenToUscore(temp);
        return temp;
    }



    public static String qualifiedToSimple(String str)
    {
        int lastDot = str.lastIndexOf(".");
        if (lastDot == -1) return str;
        return str.substring(lastDot + 1, str.length());
    }

	public static String toJavaClassName(String inString)
    {
        StringTokenizer st = new StringTokenizer(inString.toLowerCase(),PH_ULHY);
        StringBuffer outString = new StringBuffer();
         while (st.hasMoreTokens()) {
			outString.append(capFirst(st.nextToken()));
     	}
     return outString.toString();
    }

	public static String toJavaClassNameWithUscore(String inString)
	{
		return capFirst(inString);
	}

	public static String toJavaFieldName(String inString)
    {
        StringTokenizer st = new StringTokenizer(inString.toLowerCase(),HYPEN);
        StringBuffer outString = new StringBuffer();
         while (st.hasMoreTokens()) {
         	if (outString.length()==0) {
 				outString.append(st.nextToken());
         	}
         	else outString.append(capFirst(st.nextToken()));
     	}
     return outString.toString();
    }
	
	public static String toJavaPackage(String inString)
    {
        StringTokenizer st = new StringTokenizer(inString.toLowerCase(),NOT_ALLOWED_IN_JAVA_PACKAGE);
        StringBuffer outString = new StringBuffer();
         while (st.hasMoreTokens()) {
 				outString.append(st.nextToken());
     	}
        return outString.toString();
    }
	
	 public static String packageToFolder(String str)
    {

		StringBuffer buff = new StringBuffer();
		StringTokenizer st = new StringTokenizer(str,".");
		while (st.hasMoreElements()) {
			buff.append(st.nextElement());
			if (st.hasMoreElements()) {
				buff.append("\\");
			}
		}
		return buff.toString();
    }

	public static String toXSDName(String inString)
    {
        StringTokenizer st = new StringTokenizer(inString.toLowerCase(),PH_ULHY);
        StringBuffer outString = new StringBuffer(NS_PREFIX_XSD);
         while (st.hasMoreTokens()) {
         	if (outString.length()==0) {
 				outString.append(st.nextToken());
         	}
         	else outString.append(capFirst(st.nextToken()));
     	}
     return outString.toString();
    }

    public static String classToFieldName(String in)
    {
	  	if (in == null) return null;
        return (in.substring(0, 1).toLowerCase() + in.substring(1, in.length()));
    }

    public static boolean isFirstCaps(String in)
    {
	  	if (in == null) return false;
        return (in.substring(0, 1).equals(in.substring(0, 1).toUpperCase()));
    }

    public static String getPackageName(String str)
    {
        int lastDot = str.lastIndexOf(".");
        if (lastDot == -1) return str;
        return str.substring(0,lastDot);
    }

    public static String uscoretoHypen(String name) {
    	StringBuffer  buff = new StringBuffer();
    	StringTokenizer st = new StringTokenizer(name,HYPEN_REPLACER);
    	while (st.hasMoreElements()){
    		buff.append(st.nextToken());
    		if (st.hasMoreElements())  {
    			buff.append(HYPEN);
    		}
    	}
    	return buff.toString();
    }

	public static String hypenToUscore(String name) {
			StringBuffer  buff = new StringBuffer();
			StringTokenizer st = new StringTokenizer(name,"-");
			while (st.hasMoreElements()){
				buff.append(st.nextToken());
				if (st.hasMoreElements())  {
					buff.append(HYPEN_REPLACER);
				}
			}
			return buff.toString();
	}

	public static String removeExt(String fileName) {
		int index = fileName.lastIndexOf(".");
		if (index>0) return fileName.substring(0,index);
		else return fileName;
	}

	public static String removeFolder(String fileName) {
		int index = fileName.lastIndexOf("\\");
		if (index>0) return fileName.substring(index+1,fileName.length());
		else return fileName;
	}


	public static String folderToPackage(String str)
	{
		StringBuffer buff = new StringBuffer();
		StringTokenizer st = new StringTokenizer(str,"\\");
		while (st.hasMoreElements()) {
			buff.append(st.nextElement());
			if (st.hasMoreElements()) {
				buff.append(".");
			}
		}
		return buff.toString();
	  }


    public static String getFileName(File file) {
		String  standardPrefix = "com\\reap";
		int index =  file.getAbsolutePath().indexOf(standardPrefix);
		String  remainingFile="";
		if (index>0 && index+standardPrefix.length()<file.getAbsolutePath().length())  {
			remainingFile = file.getAbsolutePath().substring(index+standardPrefix.length()+1,file.getAbsolutePath().length());
		}
		String fileName="";
		if (remainingFile.trim().length()>0) {
			fileName = GeneralUtilities.folderToPackage(GeneralUtilities.removeExt(remainingFile));
		}
		else {
			fileName=GeneralUtilities.removeExt(file.getName());
		}
		return fileName;

    }

	public static String getFileName(String suffix,String remaining) {
		return packageToFolder(getClassName(suffix,remaining))+EXTENTION_JAVA;
	}


	public static String getClassName(String suffix,String remaining) {
		StringBuffer buff = new StringBuffer(REAP_PACKAGE).append(".");
		if (suffix!=null&&suffix.trim().length()>0) {
			buff.append(suffix).append(".");
		}
		buff.append(remaining);
		return buff.toString();
	}

	public static String getRelativePath(File base,File file) {
		String returnPath = null;
		returnPath = file.getAbsolutePath().substring(base.getAbsolutePath().length());
		int index = returnPath.indexOf(File.separator);
		returnPath = returnPath.substring(0,index)+"/"+returnPath.substring(index+1,returnPath.length());
		while (index>=0) {
			returnPath = returnPath.substring(0,index)+"/"+returnPath.substring(index+1,returnPath.length());
			index = returnPath.indexOf(File.separator);
		}
		return returnPath;
	}
	
	public static String printStackTrace (Object[] trace) {
		StringBuffer buffer = new StringBuffer();
		for (int i=0;i<trace.length;i++) {
			buffer.append(trace[i].toString()).append("\n");
		}
		return buffer.toString();
	}

}


/*
 * Created on Mar 11, 2006
 */
package com.vzw.cc.valueobjects;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

/**
 * @author c0narar
 */
public class SDO extends ArrayList<Object> implements Serializable, Comparable<Object>  {

	private static final long serialVersionUID = -5703212438162227589L;
    private static final String LIST_NULL = "";
    
    protected String[] attributes;
    protected String[] elements;
    protected List<String> dynamicFields;
    
    private String name;
    
    public SDO(int size) {
		super(size);
	}

	public SDO() {
		super();
	}

	public SDO(List<?> l1) {
		super();
		addAll(l1);
	}
	
	public int compareTo(Object o1) {
		return ((SDO)this).name.compareTo(((SDO)o1).name);
	}
	
	public boolean equals(Object obj) {
		return ((SDO)this).name.equals(obj);
	}
	
	public void put(String fieldName,Object val) {
		int position = getFieldPos(fieldName);
		int size = 0;
		if (position>=0) 
			put(position,val);
		else {
			if (dynamicFields == null)
				dynamicFields = new ArrayList<String>();
			dynamicFields.add(fieldName);
			if (elements != null){
				size = elements.length;
			}
			this.put((size+dynamicFields.size()-1),val);
		}
	}	
	public void replace(String fieldName,Object val) {
		int position = getFieldPos(fieldName);
		if (position>=0) {
			if (size()>position) {
				remove(position);
			}
		}
		add(position,val);
		
	}	
	
	public Object get(String fieldName) {
		int position = getFieldPos(fieldName);
		if (position >= 0&& size()>position) return get(position);
		else return null;
	}
	
	@SuppressWarnings({ "unchecked", "rawtypes" })
	private void put(int fieldPosition,Object val) {
		List<Object> tempList = null;
		if (fieldPosition >= 0) 	{
			if (val == null)
				val = LIST_NULL;
			if (size()>fieldPosition) {
				
				Object temp = get(fieldPosition);

				if (temp.equals(LIST_NULL)) {
						remove(fieldPosition);
						add(fieldPosition,val);
				} 
				else { 
				    if (temp instanceof String ||temp instanceof SDO){
				        tempList = new ArrayList<Object>();
				        tempList.add(temp);
				        set(fieldPosition,(Object)tempList);
				        temp=tempList;
					}
					if (val instanceof List&&!(val instanceof SDO)) {
					    ((List)temp).addAll((ArrayList)val);
					}
					else {
					    ((List<Object>)temp).add(val);
					}
				}
			}
			else {
				while (size()<fieldPosition) {
					add(LIST_NULL);
				}
				add(fieldPosition,val);
			}
		}
	}
	
	private int getFieldPos(String fieldName) {
	    int position=-1;
	    if (attributes!=null) {
	        for (int i=0;i<attributes.length;i++) {
	            if (attributes[i].equals(fieldName)) {
	                position = i;
	                break;
	            }
	        }
	    }

	    if (position==-1&&elements!=null) {
	        for (int i=0;i<elements.length;i++) {
	            if (elements[i].equals(fieldName)) {
	                position = i;
	                if (attributes!=null) position= position+attributes.length;
	                break;
	            }
	        }
	    }
	    
	    if (position==-1&&dynamicFields!=null) {
	        position = dynamicFields.indexOf(fieldName);
	        if (position>0) {
	            if (attributes!=null) position= position+attributes.length;
	            if (elements!=null) position= position+elements.length;
	        }
	    }
	    return position;
	}
	public String[] getAttributes() {
		return attributes;
	}
	public String[] getFields() {
	     List<String> list = new ArrayList<String>();
	 
	     if (attributes!=null) {
	         for (int i=0;i<attributes.length;i++) {
		         list.add(attributes[i]);
		     }
	     }
	     if (elements!=null) {
	         for (int i=0;i<elements.length;i++) {
	             list.add(elements[i]);
	         }
	     }
	     if (dynamicFields!=null) {
	         list.addAll(dynamicFields);
	     }
	     return list.toArray(new String[list.size()]);
	}
	
	public  String getName() {
		if (this.name==null) {
			String temp = GeneralUtilities.qualifiedToSimple(this.getClass().getName());
//			if (nameSpacePrefix!=null) temp = nameSpacePrefix+SYNT_COLAN+temp;
			return GeneralUtilities.uscoretoHypen(temp);
		}
		else {
			return GeneralUtilities.classToFieldName(this.name);
		}
	}
	public  String getParentName() {
		return GeneralUtilities.qualifiedToSimple(this.getParentName());
	}
    public void setName(String name) {
        this.name = name;
    }
    public int getAttributeSize() {
        if (attributes==null) return 0;
        return attributes.length;   
    }
    public boolean isDynamic() {
        if (attributes!=null||elements!=null) return false;
        else return true;
    }
    
    public void clear()
    {
    	
    }
    
    public int getSize()
    {
    	return getFields().length;
    }

	public String[] getElements() {
		return elements;
	}
}



/*
 * Created on Jun 18, 2006
 */
package com.vzw.cc.valueobjects;

/**
 * @author c0bha1m
 */
public class TranLog extends SDO {

	private static final long serialVersionUID = -8050630685137053766L;
	public static final String req_id = "req_id";
	public static final String seq_no = "seq_no";
    public static final String trns_type = "trns_type";
    public static final String req_input = "req_input";
    public static final String resp_output = "resp_output";
    public static final String class_name = "class_name";
    public static final String method_name = "method_name";
    public static final String req_ts = "req_ts";
    public static final String response_time = "response_time";
    public static final String status = "status";
    public static final String ext_system = "ext_system";
    public static final String date_loaded = "date_loaded";
    
    public TranLog() {
        this.elements = new String[]{req_id,seq_no,trns_type,req_input,resp_output,class_name,
        		method_name,req_ts,response_time,status,ext_system,date_loaded};   
    }
}
