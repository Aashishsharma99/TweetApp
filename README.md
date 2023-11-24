package com.vzw.cc.util;

import org.apache.commons.lang3.StringUtils;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.dom4j.Document;
import org.dom4j.Node;
import org.springframework.stereotype.Component;






@Component
public class XpathValueFinder
{
	
	private static Logger log = LogManager.getLogger(XpathValueFinder.class.getName());	
  public static final String LBL_FIELD_W_NAME_ATTRIB_NODE = "//field[@name=\"";
  public static final String LBL_FIELD_W_NAME_ATTRIB_NODE_SUFFIX = "\"]/./@value";
  public static final String LBL_ZERO_ZERO_AMOUNT = "";
  
  public static String getElementValue(Document document, String xPath) {
    String nodeValue = "";
    
    try {
      Node node = document.selectSingleNode(xPath);
      if (node == null) {
        log.debug("findElementValue(): for xPath =" + xPath + ": node NOT found.");
        log.debug("findElementValue(): Document text=" + document.getText());
      }
      else {
        
        nodeValue = node.getText();
        log.debug("findElementValue(): for xPath =" + xPath + ": node found. value =" + nodeValue);
        if (StringUtils.isEmpty(nodeValue)) {
          nodeValue = "";
        }
      } 
    } catch (Exception e) {
      if (document != null) {
        log.error("", "", "XpathValueFinder", "getElementValue", "EXCEPTION: findElementValue(): " + e.toString(), "Error", e, document.getText());
      } else {
        log.error("", "", "XpathValueFinder", "getElementValue", "EXCEPTION: findElementValue(): " + e.toString(), "Error", e);
      } 
    }  return nodeValue;
  }
  
  public static String findAttributeValue(Document document, String attributeValue) {
    String returnValue = "";
    
    try {
      String xPath = "//field[@name=\"" + attributeValue + "\"]/./@value";
      Node node = document.selectSingleNode(xPath);
      
      if (node == null) {
        log.debug("findAttributeValue(): for xPath =" + xPath + ": attribute NOT found.");
      } else {
        returnValue = node.getStringValue();
        log.debug("findAttributeValue(): for xPath =" + xPath + ": attribute found. value =" + returnValue);
        if (StringUtils.isEmpty(returnValue)) {
          returnValue = "";
        }
      } 
    } catch (Exception e) {
      log.debug("EXCEPTION: findAttributeValue(): " + e.toString());
    } 
    return returnValue;
  }
}
