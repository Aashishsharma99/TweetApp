package com.verizon.onemsg.omppservice.receiver;

import javax.jms.TextMessage;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Service;

import com.verizon.onemsg.omppservice.service.MDNCleanupService;
import com.vzw.cc.util.Encoder;

@Service
public class BatchMDNDisconnectReceiver {
	private Logger log = LogManager.getLogger(BatchMDNDisconnectReceiver.class.getName());
	
	//@Autowired
	MDNCleanupService mdnCleanupService;
	
	public BatchMDNDisconnectReceiver(@Lazy MDNCleanupService mdnCleanupService){
		this.mdnCleanupService = mdnCleanupService;
	}
	
	public void onMessage(byte[] message) {
		long startTime = System.currentTimeMillis();
		log.info(message.getClass());
		String messageTxt = new String(message);
		log.info("### OM request received as bytes:" + messageTxt);
		}

	public void onMessage(String messageTxt) {
		long startTime = System.currentTimeMillis();
		
		String xmlMsg = null;
		TextMessage txtMsg = null;
		try {
			xmlMsg = messageTxt;

			log.debug("Request XML : " + xmlMsg);
			if (xmlMsg != null) {
				xmlMsg = Encoder.makeXmlValid(xmlMsg);
				mdnCleanupService.doDisconnectMDNProcessing(xmlMsg);
				log.debug("End BatchMDNDisconnectMDB");
			}
		}catch (Exception e) {
		// TODO: handle exception
		}

		log.info("### OM request received as string:" + messageTxt);

		}
}

package com.verizon.onemsg.omppservice.receiver;

import static org.junit.jupiter.api.Assertions.assertNull;

import com.verizon.onemsg.omppservice.service.MDNCleanupService;

import java.io.UnsupportedEncodingException;

import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

@SpringBootTest
@ExtendWith(SpringExtension.class)
class BatchMDNDisconnectReceiverTest {
    @Autowired
    private BatchMDNDisconnectReceiver batchMDNDisconnectReceiver;

    void testOnMessage() {
       
        (new BatchMDNDisconnectReceiver(new MDNCleanupService())).onMessage("End BatchMDNDisconnectMDB");
    }

    @Test
    void testOnMessage2() {
        (new BatchMDNDisconnectReceiver(new MDNCleanupService())).onMessage((String) null);
    }

    @Test
    void testOnMessage3() {
       
        BatchMDNDisconnectReceiver batchMDNDisconnectReceiver = new BatchMDNDisconnectReceiver(null);
        batchMDNDisconnectReceiver.onMessage("Message Txt");
        assertNull(batchMDNDisconnectReceiver.mdnCleanupService);
    }

  
    @Test
    void testOnMessage4() {
        (new BatchMDNDisconnectReceiver(new MDNCleanupService()))
                .onMessage("com.verizon.onemsg.omppservice.receiver.BatchMDNDisconnectReceiver");
    }

    @Test
    void testOnMessage5() throws UnsupportedEncodingException {
       
        this.batchMDNDisconnectReceiver.onMessage("AAAAAAAA".getBytes("UTF-8"));
    }
}

