package com.verizon.onemsg.omppservice.listeners;

import javax.jms.Session;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.messaginghub.pooled.jms.JmsPoolConnectionFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jms.annotation.EnableJms;
import org.springframework.jms.listener.DefaultMessageListenerContainer;
import org.springframework.jms.listener.MessageListenerContainer;

import com.verizon.onemsg.omppservice.receiver.BatchDisconnectIBMQReceiver;
import com.verizon.onemsg.omppservice.receiver.MDNActivateIBMReceiver;
import com.verizon.onemsg.omppservice.receiver.MDNChangeIBMreceiver;
import com.verizon.onemsg.omppservice.receiver.RealtimeDisconnectIBMQReceiver;
import com.verizon.onemsg.omppservice.receiver.MDNReassignIBMReceiver;
import com.verizon.onemsg.omppservice.receiver.MDNTransferIBMReceiver;

@Configuration
@EnableJms
public class OMPPIBMListener {

		private static final Logger logger = LogManager.getLogger(OMPPIBMListener.class);

		@Value("${ompp.ibm.mq.concurrency:100}")
		String recieverConcurrency;

		@Value("${ompp.ibm.mq.maxmsg:-1}")
		int recieverMaxMessagesPerTask;
		
		@Value("${ompp.ibm.mq.listener.realtimemdndisconnect:OMSG.RTMDNDISCONNECT.QA}")
		String realtimeRecieverDestination;
		
		@Value("${ompp.ibm.mq.listener.batchdisconnect:OMSG.BTMDNDISCONNECT.QA}")
		String batchDisconnectRecieverDestination;
		
		@Value("${ompp.ibm.mq.listener.activate:OMSG.CUSTACTIVITYACTIVATE.QA}")
		String activateRecieverDestination;
		
		@Value("${ompp.ibm.mq.listener.mdnchange:OMSG.CUSTACTIVITYCHANGE.QA}")
		String chnageRecieverDestination;
		
		@Value("${ompp.ibm.mq.listener.mdnreassign:OMSG.CUSTACTIVITYREASSIGN.QA}")
		String reassignRecieverDestination;
		
		@Value("${ompp.ibm.mq.listener.mdntransfer:OMSG.CUSTACTIVITYTRANSFER.QA}")
		String transferRecieverDestination;
		
		@Value("${ompp.ibm.mq.realtimemdndisconnect.enable:false}")
	    private boolean enableRealtimeOmppIBMMQ;
		
		@Value("${ompp.ibm.mq.batchMdndisconnect.enable:false}")
	    private boolean enableBatchOmppIBMMQ;
		
		@Value("${ompp.ibm.mq.mdnactivate.enable:false}")
	    private boolean enableMdnActivateOmppIBMMQ;
		
		@Value("${ompp.ibm.mq.mdnchnage.enable:false}")
	    private boolean enableMdnChnageOmppIBMMQ;
		
		@Value("${ompp.ibm.mq.mdntransfer.enable:false}")
	    private boolean enableMdnTransferOmppIBMMQ;
		
		@Value("${ompp.ibm.mq.mdnreassign.enable:false}")
	    private boolean enablemdnReassignOmppIBMMQ;

		@Autowired
		MDNChangeIBMreceiver changeReceiver;
		
		@Autowired
		MDNReassignIBMReceiver reassignReceiver;
		
		@Autowired
		MDNTransferIBMReceiver transferReceiver;
		
		@Autowired
		MDNActivateIBMReceiver activateReceiver;
		
		@Autowired
		RealtimeDisconnectIBMQReceiver realtimeDisconnectReceiver;
		
		@Autowired
		BatchDisconnectIBMQReceiver batchDisconnectReceiver;
		
		

		@Bean(name = "MDNChangeListenerContainer")
		public MessageListenerContainer getChangeRequestProcessorContainer(
				JmsPoolConnectionFactory pooledConnectionFactory) {
			logger.info("Creating Mesage listener for Mailer Processor");
			DefaultMessageListenerContainer container = new DefaultMessageListenerContainer();
			container.setConnectionFactory(pooledConnectionFactory);
			container.setConcurrency(recieverConcurrency);
			container.setMaxMessagesPerTask(recieverMaxMessagesPerTask);
			container.setCacheLevel(0);
			container.setErrorHandler(t -> logger.error("An error has occurred in the transaction"));
			container.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
			container.setSessionTransacted(true);
			container.setDestinationName(chnageRecieverDestination);
			container.setMessageListener(changeReceiver);
			container.setAutoStartup(enableMdnChnageOmppIBMMQ);
			return container;
		}
		
		@Bean(name = "MDNReassignListenerContainer")
		public MessageListenerContainer getReassignRequestProcessorContainer(
				JmsPoolConnectionFactory pooledConnectionFactory) {
			logger.info("Creating Mesage listener for Mailer Processor");
			DefaultMessageListenerContainer container = new DefaultMessageListenerContainer();
			container.setConnectionFactory(pooledConnectionFactory);
			container.setConcurrency(recieverConcurrency);
			container.setMaxMessagesPerTask(recieverMaxMessagesPerTask);
			container.setCacheLevel(0);
			container.setErrorHandler(t -> logger.error("An error has occurred in the transaction"));
			container.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
			container.setSessionTransacted(true);
			container.setDestinationName(reassignRecieverDestination);
			container.setMessageListener(reassignReceiver);
			container.setAutoStartup(enablemdnReassignOmppIBMMQ);
			return container;
		}
		
		
		@Bean(name = "MDNTransferListenerContainer")
		public MessageListenerContainer getTransferRequestProcessorContainer(
				JmsPoolConnectionFactory pooledConnectionFactory) {
			logger.info("Creating Mesage listener for Mailer Processor");
			DefaultMessageListenerContainer container = new DefaultMessageListenerContainer();
			container.setConnectionFactory(pooledConnectionFactory);
			container.setConcurrency(recieverConcurrency);
			container.setMaxMessagesPerTask(recieverMaxMessagesPerTask);
			container.setCacheLevel(0);
			container.setErrorHandler(t -> logger.error("An error has occurred in the transaction"));
			container.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
			container.setSessionTransacted(true);
			container.setDestinationName(transferRecieverDestination);
			container.setMessageListener(transferReceiver);
			container.setAutoStartup(enableMdnTransferOmppIBMMQ);
			return container;
		}
		
		@Bean(name = "MDNActivateListenerContainer")
		public MessageListenerContainer getActivateRequestProcessorContainer(
				JmsPoolConnectionFactory pooledConnectionFactory) {
			logger.info("Creating Mesage listener for Mailer Processor");
			DefaultMessageListenerContainer container = new DefaultMessageListenerContainer();
			container.setConnectionFactory(pooledConnectionFactory);
			container.setConcurrency(recieverConcurrency);
			container.setMaxMessagesPerTask(recieverMaxMessagesPerTask);
			container.setCacheLevel(0);
			container.setErrorHandler(t -> logger.error("An error has occurred in the transaction"));
			container.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
			container.setSessionTransacted(true);
			container.setDestinationName(activateRecieverDestination);
			container.setMessageListener(activateReceiver);
			container.setAutoStartup(enableMdnActivateOmppIBMMQ);
			return container;
		}
		
		
		@Bean(name = "MDNRealtimeDisconnectListenerContainer")
		public MessageListenerContainer getRealtimeDisconnectRequestProcessorContainer(
				JmsPoolConnectionFactory pooledConnectionFactory) {
			logger.info("Creating Mesage listener for Mailer Processor");
			DefaultMessageListenerContainer container = new DefaultMessageListenerContainer();
			container.setConnectionFactory(pooledConnectionFactory);
			container.setConcurrency(recieverConcurrency);
			container.setMaxMessagesPerTask(recieverMaxMessagesPerTask);
			container.setCacheLevel(0);
			container.setErrorHandler(t -> logger.error("An error has occurred in the transaction"));
			container.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
			container.setSessionTransacted(true);
			container.setDestinationName(realtimeRecieverDestination);
			container.setMessageListener(realtimeDisconnectReceiver);
			container.setAutoStartup(enableRealtimeOmppIBMMQ);
			return container;
		}
		
		@Bean(name = "MDNBatchDisconnectListenerContainer")
		public MessageListenerContainer getBatchDisconnectRequestProcessorContainer(
				JmsPoolConnectionFactory pooledConnectionFactory) {
			logger.info("Creating Mesage listener for Mailer Processor");
			DefaultMessageListenerContainer container = new DefaultMessageListenerContainer();
			container.setConnectionFactory(pooledConnectionFactory);
			container.setConcurrency(recieverConcurrency);
			container.setMaxMessagesPerTask(recieverMaxMessagesPerTask);
			container.setCacheLevel(0);
			container.setErrorHandler(t -> logger.error("An error has occurred in the transaction"));
			container.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
			container.setSessionTransacted(true);
			container.setDestinationName(batchDisconnectRecieverDestination);
			container.setMessageListener(batchDisconnectReceiver);
			container.setAutoStartup(enableBatchOmppIBMMQ);
			return container;
		}

	}


