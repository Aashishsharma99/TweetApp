package com.vzw.cc.util;

import com.verizon.onemsg.omppservice.util.mail.MailBoxConnector;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.URL;
import java.net.URLConnection;
import java.util.HashMap;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.stereotype.Component;
 




@Component
public class HttpCaller
{
	private static Logger log = LogManager.getLogger(HttpCaller.class.getName());	  
  public static String makeCall(String urlString, String data) throws Exception {
    log.info("makeCall(): STARTED. Using HttpCallDao url=" + urlString);
    log.info("query data=" + data);
    String totalResponse = "";
    int equalIndex = -1;
    if ((equalIndex = data.indexOf('=')) != -1) {
      HashMap<String, String> hm = new HashMap<String, String>();
      hm.put(data.substring(0, equalIndex), data.substring(equalIndex + 1, data.length()));
      totalResponse = HttpCallDao.callHttpPost(urlString, hm);
    } else {
      log.info("makeCall(): Aborted: xmlreqdoc= is required in data");
    }  log.info("makeCall(): DONE.");
    return totalResponse;
  }









  
  public static String makeCallOld(String urlString, String data) throws Exception {
    log.info("makeCall(): STARTED. url=" + urlString);
    log.info("query data=" + data);
    String totalResponse = "";
    String responseReceived = "";
    
    try {
      URL url = new URL(urlString);
      URLConnection conn = url.openConnection();
      conn.setDoOutput(true);
      OutputStreamWriter wr = new OutputStreamWriter(conn.getOutputStream());
      wr.write(data);
      wr.flush();

      
      StringBuffer sb = new StringBuffer();
      BufferedReader rd = new BufferedReader(new InputStreamReader(conn.getInputStream()));
      
      while ((responseReceived = rd.readLine()) != null) {
        sb.append(responseReceived);
      }
      wr.close();
      rd.close();
      
      totalResponse = sb.toString();
    }
    catch (Exception e) {
      log.warn("makeCall(): EXCEPTION: " + e.toString());
      responseReceived = e.toString();
      throw e;
    } 
    log.info("makeCall(): DONE.");

    
    return totalResponse;
  }
}
