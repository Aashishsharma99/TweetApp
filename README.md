package com.vzw.cc.util;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.URL;
import java.net.URLConnection;
import java.net.URLDecoder;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

import org.apache.commons.httpclient.DefaultHttpMethodRetryHandler;
import org.apache.commons.httpclient.Header;
import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.NameValuePair;
import org.apache.commons.httpclient.URI;
import org.apache.commons.httpclient.methods.GetMethod;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;
import org.apache.commons.httpclient.params.HttpClientParams;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.stereotype.Component;





@Component
public class HttpCallDao
{
	private static Logger log = LogManager.getLogger(HttpCallDao.class.getName());
  private static final int HTTP_TIME_OUT = 6000;
  
  public static String callHttpGet(String urlString, String queryData, int timeOut) {
    HttpClientParams hcParams = new HttpClientParams();
    hcParams.setSoTimeout(timeOut);
    return callHttpGet(urlString, queryData, hcParams);
  }




  
  public static String callHttpGet(String urlString, String queryData) {
    HttpClientParams hcParams = new HttpClientParams();
    hcParams.setSoTimeout(6000);
    hcParams.setConnectionManagerTimeout(6000L);
    return callHttpGet(urlString, queryData, hcParams);
  }

  
  public static String callHttpGet(String urlString, String queryData, HttpClientParams hcParams) {
    HttpClient client = new HttpClient(hcParams);
    GetMethod httpcall = new GetMethod(urlString);
    httpcall.setQueryString(queryData);
    String responseString = "";
    
    try {
      log.debug("Making http call");
      client.executeMethod(httpcall);
      log.debug("http status = " + httpcall.getStatusCode());
      if (httpcall.getStatusCode() == 200) {
        responseString = httpcall.getResponseBodyAsString();
      } else {
        
        String errString = httpcall.getStatusLine().toString();
        log.error("MakingHttpCall", "HttpCallDao", "callHttpGet", "Error: callHttpGet(): " + errString, "Error", null);
      } 
    } catch (Exception e) {
      log.error("MakingHttpCall", "HttpCallDao", "callHttpGet", "EXCEPTION: callHttpGet(): " + e.toString(), "Error", e);
    } finally {
      httpcall.releaseConnection();
    } 
    return responseString;
  }
  
  public static String callHttpPost(String urlString, String data) {
    int equalIndex = -1;
    String responseString = "";
    if ((equalIndex = data.indexOf('=')) != -1) {
      HashMap<String, String> hm = new HashMap<String, String>();
      hm.put(data.substring(0, equalIndex), data.substring(equalIndex + 1, data.length()));
      HttpClientParams hcParams = new HttpClientParams();
      responseString = callHttpPost(urlString, hm, hcParams);
    } else {
      log.info("callHttpPost: Aborted: xmlreqdoc= is required in data");
    }  return responseString;
  }
  
  public static String callHttpPost(String urlString, String data, int timeOut) {
    int equalIndex = -1;
    String responseString = "";
    if ((equalIndex = data.indexOf('=')) != -1) {
      HashMap<String, String> hm = new HashMap<String, String>();
      hm.put(data.substring(0, equalIndex), data.substring(equalIndex + 1, data.length()));
      HttpClientParams hcParams = new HttpClientParams();
      responseString = callHttpPost(urlString, hm, hcParams, timeOut);
    } else {
      log.info("callHttpPost: Aborted: xmlreqdoc= is required in data");
    }  return responseString;
  }
  
  public static String callHttpPost(String urlString, Map<String, String> queryData) {
    HttpClientParams hcParams = new HttpClientParams();
    return callHttpPost(urlString, queryData, hcParams);
  }

  
  public static String callHttpPost(String urlString, Map<String, String> queryData, int timeOut) {
    HttpClientParams hcParams = new HttpClientParams();
    return callHttpPost(urlString, queryData, hcParams, timeOut);
  }


  
  public static String callHttpPost(String urlString, Map<String, String> queryData, HttpClientParams hcParams) { return callHttpPost(urlString, queryData, hcParams, 6000); }

  
  public static String callHttpPost(String urlString, int timeOut) {
    HttpClientParams hcParams = new HttpClientParams();
    HashMap<String, String> hm = new HashMap<String, String>();
    return callHttpPost(urlString, hm, hcParams, timeOut);
  }
  
  public static String callHTTPBodyPost(String urlString, String body, int timeOut) {
    HttpClientParams hcParams = new HttpClientParams();
    hcParams.setSoTimeout(timeOut);
    hcParams.setConnectionManagerTimeout(timeOut);
    HttpClient client = new HttpClient(hcParams);
    
    client.setConnectionTimeout(timeOut);
    PostMethod httpcall = new PostMethod(urlString);
    String responseString = "";
    try {
      httpcall.setRequestEntity(new StringRequestEntity(body, null, null));
      client.executeMethod(httpcall);
      if (httpcall.getStatusCode() == 200) {
        responseString = httpcall.getResponseBodyAsString();
      } else {
        String errString = httpcall.getStatusLine().toString();
        log.error("MakingHttpCall", "HttpCallDao", "callHTTPBodyPost", "Error: callHTTPBodyPost(): " + errString, "Error", null);
      } 
    } catch (Exception e) {
      log.error("MakingHttpCall", "HttpCallDao", "callHTTPBodyPost", "EXCEPTION: callHTTPBodyPost(): " + e.toString(), "Error", e);
    } finally {
      httpcall.releaseConnection();
    } 
    return responseString;
  }








  
  public static String callHttpPost(String urlString, Map<String, String> queryData, HttpClientParams hcParams, int timeOut) {
    hcParams.setSoTimeout(timeOut);
    hcParams.setConnectionManagerTimeout(timeOut);
    HttpClient client = new HttpClient(hcParams);
    client.setConnectionTimeout(timeOut);
    PostMethod httpcall = new PostMethod(urlString);
    Iterator<String> it1 = queryData.keySet().iterator();
    String key = null;
    while (it1.hasNext()) {
      key = (String)it1.next();
      httpcall.setParameter(key, (String)queryData.get(key));
    } 
    String responseString = "";
    try {
      log.debug("Making http call");
      client.executeMethod(httpcall);
      log.debug("http status = " + httpcall.getStatusCode());
      if (httpcall.getStatusCode() == 200)
      { responseString = httpcall.getResponseBodyAsString(); }
      
      else if (httpcall.getStatusCode() == 301 || httpcall.getStatusCode() == 302 || 
        httpcall.getStatusCode() == 307)
      
      { String redirectLocation = "";
        String redirectQueryData = "";
        Header locationHeader = httpcall.getResponseHeader("location");
        if (locationHeader != null) {
        	PostMethod redirectHTTPCall = new PostMethod();
          try {
            log.debug("HTTP location header: " + locationHeader);
            redirectLocation = locationHeader.getValue();
            int queryIndex = -1;
            
            redirectQueryData = URLDecoder.decode(redirectLocation.substring(queryIndex + 1, redirectLocation.length()), "UTF-8");
            log.debug("redirectQueryData: " + redirectQueryData);
            redirectLocation = redirectLocation.substring(0, queryIndex);
            int equalIndex = -1;
            if ((queryIndex = redirectLocation.indexOf('?')) != -1 && (equalIndex = redirectQueryData.indexOf('=')) != -1) {
              String redirectQueryDataName = redirectQueryData.substring(0, equalIndex);
              String redirectQueryDataValue = redirectQueryData.substring(equalIndex + 1, redirectQueryData.length());
              log.debug("redirectQueryDataName: " + redirectQueryDataName);
              log.debug("redirectQueryDataValue: " + redirectQueryDataValue);
              redirectHTTPCall.setParameter(redirectQueryDataName, redirectQueryDataValue);
              NameValuePair[] nvp = redirectHTTPCall.getParameters();
              log.debug("length(NameValuePair) = " + nvp.length);
              for (int i = 0; i < nvp.length; i++) {
                log.debug("NameValuePair[" + i + "] " + nvp[i].getName() + " = " + nvp[i].getValue());
              }
            } 
            
            redirectHTTPCall.setURI(new URI(redirectLocation, false));
            log.debug("Making http redirect call to " + redirectLocation);
            client.executeMethod(redirectHTTPCall);
            log.debug("http status = " + redirectHTTPCall.getStatusCode());
            if (redirectHTTPCall.getStatusCode() == 200) {
              responseString = redirectHTTPCall.getResponseBodyAsString();
            } else {
              String errString = redirectHTTPCall.getStatusLine().toString();
              log.error("MakingHttpCall redirect", "HttpCallDao", "callHttpGet", "Error: callHttpGet(): " + errString, "Error", null);
            } 
          } catch (Exception e) {
            log.error("MakingHttpCall redirect", "HttpCallDao", "callHttpGet", "EXCEPTION: callHttpGet(): " + e.toString(), "Error", e);
          } finally {
            redirectHTTPCall.releaseConnection();
          } 
        } else {
          log.info("MakingHttpCall: got redirect without location.");
        }  }
      else { String errString = httpcall.getStatusLine().toString();
        log.error("MakingHttpCall", "HttpCallDao", "callHttpGet", "Error: callHttpGet(): " + errString, "Error", null); }

    
    } catch (Exception e) {
      log.error("MakingHttpCall", "HttpCallDao", "callHttpGet", "EXCEPTION: callHttpGet(): " + e.toString(), "Error", e);
    } finally {
      httpcall.releaseConnection();
    } 
    return responseString;
  }

  
  public static String SubmitXmlToUrl(String submitUrl, String inXML) {
    String Tresponse = null;
    
    PrintWriter out = null;
    BufferedReader in = null;
    URL subUrl = null;
    URLConnection subUrlConnection = null;
    try {
      log.debug("Submit URL: " + submitUrl);
      log.debug("Request XML submitted: " + inXML);
      
      subUrl = new URL(submitUrl);
      subUrlConnection = subUrl.openConnection();
      
      subUrlConnection.setDoOutput(true);
      subUrlConnection.setUseCaches(false);
      subUrlConnection.setRequestProperty("Content-Length", 
          Integer.toString(inXML.length()));
      subUrlConnection.setRequestProperty("Content-Type", "text/xml");
      out = new PrintWriter(subUrlConnection.getOutputStream());
      out.println(inXML);
      out.flush();
      out.close();
    } catch (Exception e) {
      
      log.debug("Unable to open the URL: " + e.getMessage());
      log.error("MakingHttpCall", "HttpCallDao", "SubmitXmlToUrl", "EXCEPTION: SubmitXmlToUrl(): " + e.toString(), "Error", e);
    } 
    
    try {
      in = new BufferedReader(new InputStreamReader(subUrlConnection.getInputStream()));
      StringBuffer ToutputStr = new StringBuffer();
      String ToutputString;
      while ((ToutputString = in.readLine()) != null) {
        ToutputStr.append(ToutputString);
      }
      Tresponse = ToutputStr.toString();
    } catch (Exception e) {
      
      log.debug("Response Error: " + in + e.getMessage());
      log.error("MakingHttpCall", "HttpCallDao", "SubmitXmlToUrl", "EXCEPTION: SubmitXmlToUrl(): " + e.toString(), "Error", e);
    } 
    return Tresponse;
  }
  
  public static String SubmitXmlToUrlWithTimeOut(String urlString, String queryData, int timeOut) {
    String responseString = "";
    PostMethod httpPostCall = new PostMethod(urlString);
    try {
      HttpClientParams hcParams = new HttpClientParams();
      hcParams.setSoTimeout(timeOut);
      HttpClient client = new HttpClient(hcParams);
      StringRequestEntity stringRequestEntity = new StringRequestEntity(queryData, "text/xml", "UTF-8");
      httpPostCall.setRequestEntity(stringRequestEntity);
      
      log.debug("Making http post call");
      client.executeMethod(httpPostCall);
      
      log.debug("http status = " + httpPostCall.getStatusCode());
      if (httpPostCall.getStatusCode() == 200) {
        responseString = httpPostCall.getResponseBodyAsString();
      } else {
        String errString = httpPostCall.getStatusLine().toString();
        System.out.println("httpPostCall.getStatusLine().toString() : " + errString);
      }
    
    } catch (Exception e) {
      System.out.println("Exception occured while calling" + e.toString());
    } finally {
      
      httpPostCall.releaseConnection();
    } 
    return responseString;
  }
  
  public static String SubmitXmlToUrlWithTimeOutAndRetry(String urlString, String queryData, int timeOut, int retryCount) {
    String responseString = "";
    PostMethod httpPostCall = new PostMethod(urlString);
    try {
      HttpClientParams hcParams = new HttpClientParams();
      hcParams.setSoTimeout(timeOut);
      hcParams.setConnectionManagerTimeout(timeOut);
      hcParams.setParameter("http.method.retry-handler", new DefaultHttpMethodRetryHandler(retryCount, false));
      
      HttpClient client = new HttpClient(hcParams);
      StringRequestEntity stringRequestEntity = new StringRequestEntity(queryData, "text/xml", "UTF-8");
      httpPostCall.setRequestEntity(stringRequestEntity);
      
      log.debug("Making http post call");
      client.executeMethod(httpPostCall);
      
      log.debug("http status = " + httpPostCall.getStatusCode());
      if (httpPostCall.getStatusCode() == 200) {
        responseString = httpPostCall.getResponseBodyAsString();
      } else {
        String errString = httpPostCall.getStatusLine().toString();
        System.out.println("httpPostCall.getStatusLine().toString() : " + errString);
      }
    
    } catch (Exception e) {
      System.out.println("Exception occured while calling" + e.toString());
    } finally {
      
      httpPostCall.releaseConnection();
    } 
    return responseString;
  }






  
  public static String submitXmlToUrlWithTimeOutAndRetryWithTransId(String urlString, String queryData, int timeOut, int retryCount, String transId) {
    log.info(" submit  With Trans Id  as parameter");
    String responseString = "";
    PostMethod httpPostCall = new PostMethod(String.valueOf(urlString) + "?transId=" + transId);
    
    try {
      HttpClientParams hcParams = new HttpClientParams();
      hcParams.setSoTimeout(timeOut);
      hcParams.setConnectionManagerTimeout(timeOut);
      hcParams.setParameter("http.method.retry-handler", new DefaultHttpMethodRetryHandler(retryCount, false));

      
      HttpClient client = new HttpClient(hcParams);
      StringRequestEntity stringRequestEntity = new StringRequestEntity(queryData, null, "UTF-8");
      
      httpPostCall.setRequestEntity(stringRequestEntity);
      
      log.info(" Making http post call");
      client.executeMethod(httpPostCall);
      
      log.info(" http status = " + httpPostCall.getStatusCode());
      if (httpPostCall.getStatusCode() == 200) {
        responseString = httpPostCall.getResponseBodyAsString();
      } else {
        String errString = httpPostCall.getStatusLine().toString();
        log.info("Anila: httpPostCall.getStatusLine().toString() : " + errString);
      }
    
    } catch (Exception e) {
      log.info("Anila: Exception occured while calling" + e.toString());
    } finally {
      
      httpPostCall.releaseConnection();
    } 
    return responseString;
  }
}
