package com.vzw.cc.util;

import java.util.ArrayList;
import java.util.List;

import org.springframework.stereotype.Component;

@Component
public class UniqueIdGenerator
{
  private static long oldTime = 0L;
  private static long offset = 0L;

  
  private static List<String> getUniqId(int totalUniqNosNeeded) {
    String currTime = String.valueOf(System.currentTimeMillis());
    String currThread = Thread.currentThread().getName();
    
    String serverId = System.getProperty("server.id");
    
    serverId = getLastTwoDigits(serverId);
    currThread = getLastTwoDigits(currThread);
    List<String> uniqIds = new ArrayList<String>();
    
    for (int index = 0; index < totalUniqNosNeeded; index++) {
      
      if (System.currentTimeMillis() != oldTime) {
        
        currTime = String.valueOf(System.currentTimeMillis());
        uniqIds.add(String.valueOf(currTime) + currThread + serverId + "00");
        offset = 1L;
        oldTime = System.currentTimeMillis();
      } else {
        
        uniqIds.add(String.valueOf(currTime) + currThread + serverId + ((offset % 10L == offset) ? ("0" + offset) : String.valueOf(offset)));
        offset++;
      } 
    } 
    
    return uniqIds;
  }

  
  private static List<String> get21DigitUniqId(int totalUniqNosNeeded) {
    String currTime = String.valueOf(System.nanoTime());
    String currThread = Thread.currentThread().getName();
    
    String serverId = System.getProperty("server.id");
    
    serverId = getLastTwoDigits(serverId);
    currThread = getLastTwoDigits(currThread);
    List<String> uniqIds = new ArrayList<String>();
    
    for (int index = 0; index < totalUniqNosNeeded; index++) {
      
      if (System.nanoTime() != oldTime) {
        
        currTime = String.valueOf(System.nanoTime());
        uniqIds.add(String.valueOf(currTime) + currThread + serverId + "00");
        offset = 1L;
        oldTime = System.nanoTime();
      } else {
        
        uniqIds.add(String.valueOf(currTime) + currThread + serverId + ((offset % 10L == offset) ? ("0" + offset) : String.valueOf(offset)));
        offset++;
      } 
    } 
    
    return uniqIds;
  }

  
  private static String getLastTwoDigits(String id) {
    if (id != null && Character.isDigit(id.charAt(id.length() - 1))) {
      if (Character.isDigit(id.charAt(id.length() - 2))) {
        return id.substring(id.length() - 2);
      }
      return "0" + id.substring(id.length() - 1);
    } 
    return "00";
  }


  
  public static String getUniqId() { return (String)getUniqId(1).get(0); }



  
  public static String get21DigitUniqId() { return (String)get21DigitUniqId(1).get(0); }



  
  public static List<String> getUniqIds(int totalUniqNosNeeded) { return getUniqId(totalUniqNosNeeded); }

  
  public static void main(String[] args) {
    System.out.println(getUniqId());
    System.out.println(getUniqId());
    System.out.println(getUniqId());
    System.out.println(getUniqId());
    System.out.println(getUniqId());
  }
}
