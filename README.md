package com.verizon.onemsg.omppservice.controller;

import javax.jms.DeliveryMode;

import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cache.CacheManager;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.ModelAndView;

import com.ibm.mq.jms.MQQueue;
import com.ibm.msg.client.wmq.WMQConstants;
import com.verizon.onemsg.omppservice.util.DateUtil;

@RestController
public class OMPPSericeController {
	
	private static final Logger log = LoggerFactory.getLogger(OMPPSericeController.class);
	
	@Value("${RMQ_EXCHANGE}")
	String exchange;
	
	@Value("${RMQ_OMPPS_ROUTING_KEY}")
	String routingKey;
	
	@Value("${RMQ_BATCH_DISCONNECT_PROCESS_QUEUE}")
	String queueName;
	@Autowired
	RabbitTemplate rabbitTemplate;
	
	@Autowired
	JmsTemplate jmsTemplate;
	
	@Autowired
	CacheManager cacheManager;

	@RequestMapping("/jsp/keepalive.jsp")
	public String status() {
		return "Success";
	}
	
	@PostMapping("/OMPPSQueuePosterString")
	public @ResponseBody String postToSepQueueString(@RequestBody(required=false) String message, @RequestParam(name="xmlreqdoc", required=false) String xmlreqdoc,  @RequestParam(name="routekey", required=false) String routeKey){
		log.info("Inside OMPPS Queue poster");
		log.info("## RMQ_EXCHANGE:"+exchange+" - Posting to ROUTING_KEY:"+routeKey );
		if(null != xmlreqdoc && !xmlreqdoc.isEmpty() && StringUtils.isNotBlank(routeKey)) {
			message = xmlreqdoc;
			rabbitTemplate.convertAndSend(exchange, routeKey, message);
		}else {
			return "Failure !!! XML and routingkey is mandatory";
		}
		return "Success";
	}
	@PostMapping("/LegacyQueuePoster")
	public @ResponseBody String postToLegacyQueue(@RequestBody(required=false) String message, @RequestParam(name="xmlreqdoc", required=false) String xmlreqdoc,  @RequestParam(name="queue", required=false) String queue){
		log.info("Inside OMPPS Queue poster");
		log.info("## Posting to IBMQ_:"+exchange+" - Posting to QueueName:"+queue );
		MQQueue queueDest = null;
		try {
			queueDest = new MQQueue(queue);
			queueDest.setPutAsyncAllowed(WMQConstants.WMQ_PUT_ASYNC_ALLOWED_ENABLED);
			queueDest.setPersistence(DeliveryMode.NON_PERSISTENT);
		}catch(Exception ex){
			log.error("Unable to create queue destination: ", ex);
			queueName = null;
		}
		if(null != xmlreqdoc && !xmlreqdoc.isEmpty() && StringUtils.isNotBlank(queue)) {
			message = xmlreqdoc;
			jmsTemplate.convertAndSend(queueDest, message);
		}else {
			return "Failure !!! XML and Queuename is mandatory";
		}
		return "Success";
	}
	
	@RequestMapping(value="/PostingToQueue", method = RequestMethod.GET)
    public ModelAndView postingToQueue(ModelMap model){
		ModelAndView view = new ModelAndView("TestVisionActivity");
        return view;
    }
	@RequestMapping("/PostingToQueue.jsp")
	public ModelAndView PostingToQueue() {
		ModelAndView view = new ModelAndView("TestVisionActivity");
		return view;
	}
	@RequestMapping("/PostingToLegacyQueue.jsp")
	public ModelAndView PostingToLegacyQueue() {
		ModelAndView view = new ModelAndView("LegacyQActivity");
		return view;
	}
	
	@GetMapping("/clearCache") // To clear cache by name from database Caching
    public @ResponseBody String clearCacheByname(@RequestParam(name="cacheName", required=false) String cacheName) {
		if(DateUtil.isNotNullOrEmpty(cacheName)) {
			cacheManager.getCache(cacheName).clear();
		}
		return "Success";
    }
}
