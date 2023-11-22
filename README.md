package com.verizon.onemsg.omppservice.properties;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

@RefreshScope
@Component
public class AppProperties {
	
	@Autowired
	Environment environment;

	

	/**
	 * Return the property value associated with the given key, or null if the
	 * key cannot be resolved.
	 * 
	 * @param propName
	 * @return
	 */
    
	public String getProperty(String key) {

		String propValue = environment.getProperty(key);
		
		return propValue != null ? propValue.trim() : propValue; 
	}

	
	/**
	 * Return the property value associated with the given key, or defaultValue
	 * if the key cannot be resolved.
	 * 
	 * @param propName
	 * @param defaultValue
	 * @return
	 */
	public String getProperty(String key,String defaultValue) {

		final String propValue =  environment.getProperty(key);
		
		return propValue != null ? propValue : defaultValue;
		
	}
}
