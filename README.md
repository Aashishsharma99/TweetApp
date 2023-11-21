package com.verizon.onemsg.omppservice.config;

import javax.jms.DeliveryMode;

import org.messaginghub.pooled.jms.JmsPoolConnectionFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.jms.connection.JmsTransactionManager;
import org.springframework.jms.connection.UserCredentialsConnectionFactoryAdapter;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.transaction.PlatformTransactionManager;

import com.ibm.mq.jms.MQQueueConnectionFactory;
import com.ibm.msg.client.wmq.WMQConstants;

@RefreshScope
@Configuration
@EnableAutoConfiguration(exclude=JmsAutoConfiguration.class)
public class IBMMQConfig {

	@Value("${ompp.ibm.mq.host}")
    private String host;
    @Value("${ompp.ibm.mq.port}")
    private Integer port;
    @Value("${ompp.ibm.mq.queue-manager}")
    private String queueManager;
    @Value("${ompp.ibm.mq.channel}")
    private String channel;
    @Value("${ompp.ibm.mq.username}")
    private String username;
    @Value("${ompp.ibm.mq.password}")
    private String password;
    @Value("${ompp.ibm.mq.receive-timeout}")
    private long receiveTimeout;
    
    @Value("${ompp.ibm.mq.pool.idleTimeout}")
    private int idleTimeout;
    @Value("${ompp.ibm.mq.pool.maxConnections}")
    private int maxConnections;
    @Value("${ompp.ibm.mq.pool.maxSessionsPerConnection}")
    private int maxSessionsPerConnection;
    
    /*@Bean
    public JmsListenerContainerFactory<?> myFactory(JmsPoolConnectionFactory pooledConnectionFactory
                                                    ,DefaultJmsListenerContainerFactoryConfigurer configurer) {
        DefaultJmsListenerContainerFactory factory = new DefaultJmsListenerContainerFactory();
        // This provides all boot's default to this factory, including the message converter
       // configurer.configure(factory, mqQueueConnectionFactory);
        // You could still override some of Boot's default if necessary.
        factory.setConnectionFactory(pooledConnectionFactory);
        factory.setAutoStartup(enableSepIBMMQ);
        return factory;
    }*/

    @Bean
    public MQQueueConnectionFactory mqQueueConnectionFactory() {
        MQQueueConnectionFactory mqQueueConnectionFactory = new MQQueueConnectionFactory();
        try {
        	mqQueueConnectionFactory.setConnectionNameList(host);
            mqQueueConnectionFactory.setTransportType(WMQConstants.WMQ_CM_CLIENT);
        	//mqQueueConnectionFactory.setTransportType(WMQConstants.WMQ_CM_BINDINGS);
            mqQueueConnectionFactory.setCCSID(1208);
            mqQueueConnectionFactory.setChannel(channel);
            //mqQueueConnectionFactory.setPort(port);
            mqQueueConnectionFactory.setQueueManager(queueManager);
            mqQueueConnectionFactory.setIntProperty(WMQConstants.WMQ_PUT_ASYNC_ALLOWED, WMQConstants.WMQ_PUT_ASYNC_ALLOWED_ENABLED);
            //mqQueueConnectionFactory.setBooleanProperty(WMQConstants.USER_AUTHENTICATION_MQCSP, false);

        } catch (Exception e) {
            e.printStackTrace();
        }
        return mqQueueConnectionFactory;
    }

    @Bean
    public UserCredentialsConnectionFactoryAdapter userCredentialsConnectionFactoryAdapter(MQQueueConnectionFactory mqQueueConnectionFactory) {
        UserCredentialsConnectionFactoryAdapter userCredentialsConnectionFactoryAdapter = new UserCredentialsConnectionFactoryAdapter();
        userCredentialsConnectionFactoryAdapter.setUsername(username);
        userCredentialsConnectionFactoryAdapter.setPassword(password);
        userCredentialsConnectionFactoryAdapter.setTargetConnectionFactory(mqQueueConnectionFactory);
        return userCredentialsConnectionFactoryAdapter;
    }

    @Bean
    @Primary
    public JmsPoolConnectionFactory pooledConnectionFactory(UserCredentialsConnectionFactoryAdapter userCredentialsConnectionFactoryAdapter) {
    	JmsPoolConnectionFactory pooledConnectionFactory = new JmsPoolConnectionFactory();
    	pooledConnectionFactory.setConnectionFactory(userCredentialsConnectionFactoryAdapter);
    	pooledConnectionFactory.setConnectionIdleTimeout(idleTimeout);
    	pooledConnectionFactory.setMaxConnections(maxConnections);
    	pooledConnectionFactory.setMaxSessionsPerConnection(maxSessionsPerConnection);
    	
        return pooledConnectionFactory;
    }

    @Bean
    public PlatformTransactionManager jmsTransactionManager(JmsPoolConnectionFactory pooledConnectionFactory) {
        JmsTransactionManager jmsTransactionManager = new JmsTransactionManager();
        jmsTransactionManager.setConnectionFactory(pooledConnectionFactory);
        
        return jmsTransactionManager;
    }

    @Bean
    public JmsTemplate jmsTemplate(JmsPoolConnectionFactory pooledConnectionFactory) {
        JmsTemplate jmsTemplate = new JmsTemplate(pooledConnectionFactory);
        jmsTemplate.setReceiveTimeout(receiveTimeout);
        
        jmsTemplate.setDeliveryMode(DeliveryMode.NON_PERSISTENT);
		jmsTemplate.setDeliveryPersistent(false);
		jmsTemplate.setSessionTransacted(false);
		
        return jmsTemplate;
    }
    
    
}

	

