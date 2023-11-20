import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.cache.Cache;
import org.springframework.cache.CacheManager;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.ui.ModelMap;
import org.springframework.web.servlet.ModelAndView;

@ExtendWith(MockitoExtension.class)
class OMPPSericeControllerTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    @Mock
    private JmsTemplate jmsTemplate;

    @Mock
    private CacheManager cacheManager;

    @Mock
    private Cache cache;

    @InjectMocks
    private OMPPSericeController omppSericeController;

    @Test
    void testStatus() {
        // Act
        String result = omppSericeController.status();

        // Assert
        assertEquals("Success", result);
    }

    @Test
    void testPostToSepQueueString_Success() {
        // Arrange
        String xmlreqdoc = "<xml>test</xml>";
        String routeKey = "testRoute";

        // Act
        String actualResponse = omppSericeController.postToSepQueueString(xmlreqdoc, xmlreqdoc, routeKey);

        // Assert
        assertEquals("Success", actualResponse);
        verify(rabbitTemplate, times(1)).convertAndSend(any(), any(), any());
    }

    @Test
    void testPostToSepQueueString_Failure() {
        // Act
        String actualResponse = omppSericeController.postToSepQueueString(null, null, null);

        // Assert
        assertEquals("Failure !!! XML and routingkey is mandatory", actualResponse);
        verify(rabbitTemplate, never()).convertAndSend(any(), any(), any());
    }

    @Test
    void testPostToLegacyQueue_Success() {
        // Arrange
        String xmlreqdoc = "<xml>test</xml>";
        String queueName = "testQueue";

        // Act
        String actualResponse = omppSericeController.postToLegacyQueue(xmlreqdoc, xmlreqdoc, queueName);

        // Assert
        assertEquals("Success", actualResponse);
        verify(jmsTemplate, times(1)).convertAndSend(any(), any());
    }

    @Test
    void testPostToLegacyQueue_Failure() {
        // Act
        String actualResponse = omppSericeController.postToLegacyQueue(null, null, null);

        // Assert
        assertEquals("Failure !!! XML and Queuename is mandatory", actualResponse);
        verify(jmsTemplate, never()).convertAndSend(any(), any());
    }

    @Test
    void testPostingToQueue() {
        // Act
        ModelAndView result = omppSericeController.postingToQueue(new ModelMap());

        // Assert
        assertEquals("TestVisionActivity", result.getViewName());
    }

    @Test
    void testPostingToQueueJsp() {
        // Act
        ModelAndView result = omppSericeController.PostingToQueue();

        // Assert
        assertEquals("TestVisionActivity", result.getViewName());
    }

    @Test
    void testPostingToLegacyQueueJsp() {
        // Act
        ModelAndView result = omppSericeController.PostingToLegacyQueue();

        // Assert
        assertEquals("LegacyQActivity", result.getViewName());
    }

    @Test
    void testClearCacheByname_Success() {
        // Arrange
        String cacheName = "testCache";

        when(cacheManager.getCache(cacheName)).thenReturn(cache);

        // Act
        String actualResponse = omppSericeController.clearCacheByname(cacheName);

        // Assert
        assertEquals("Success", actualResponse);
        verify(cache, times(1)).clear();
    }

    @Test
    void testClearCacheByname_CacheNotFound() {
        // Arrange
        String cacheName = "nonexistentCache";

        when(cacheManager.getCache(cacheName)).thenReturn(null);

        // Act
        String actualResponse = omppSericeController.clearCacheByname(cacheName);

        // Assert
        assertEquals("Success", actualResponse); // It should still return "Success" as there is no cache to clear
        verify(cache, never()).clear();
    }
}
