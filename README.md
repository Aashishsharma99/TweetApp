import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.HashMap;
import java.util.Map;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.HttpMethod;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.params.HttpClientParams;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.boot.test.context.SpringBootTest;

@ExtendWith(MockitoExtension.class)
@SpringBootTest
class HttpCallDaoTest {

    @Mock
    private HttpClient httpClient;

    @InjectMocks
    private HttpCallDao httpCallDao;

    @Test
    void callHttpGet_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String queryData = "param1=value1&param2=value2";
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(HttpMethod.class))).thenReturn(200);
        when(httpClient.getResponseBodyAsString()).thenReturn("Success Response");

        // Act
        String result = httpCallDao.callHttpGet(urlString, queryData, hcParams);

        // Assert
        assertEquals("Success Response", result);
    }

    @Test
    void callHttpGet_Failure() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String queryData = "param1=value1&param2=value2";
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(HttpMethod.class))).thenReturn(500);

        // Act and Assert
        assertThrows(HttpCallException.class, () -> httpCallDao.callHttpGet(urlString, queryData, hcParams));
    }

    @Test
    void callHttpPost_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param1=value1&param2=value2";
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(PostMethod.class))).thenReturn(200);
        when(httpClient.getResponseBodyAsString()).thenReturn("Success Response");

        // Act
        String result = httpCallDao.callHttpPost(urlString, data, hcParams);

        // Assert
        assertEquals("Success Response", result);
    }

    @Test
    void callHttpPost_Failure() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param1=value1&param2=value2";
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(PostMethod.class))).thenReturn(500);

        // Act and Assert
        assertThrows(HttpCallException.class, () -> httpCallDao.callHttpPost(urlString, data, hcParams));
    }

    @Test
    void SubmitXmlToUrl_Success() {
        // Arrange
        String submitUrl = "http://example.com";
        String inXML = "<xml>data</xml>";
        when(httpClient.getResponseBodyAsString()).thenReturn("Success Response");

        // Act
        String result = httpCallDao.SubmitXmlToUrl(submitUrl, inXML);

        // Assert
        assertEquals("Success Response", result);
    }

    @Test
    void SubmitXmlToUrl_Failure() {
        // Arrange
        String submitUrl = "http://example.com";
        String inXML = "<xml>data</xml>";
        when(httpClient.getResponseBodyAsString()).thenReturn("Error Response");

        // Act and Assert
        assertThrows(HttpCallException.class, () -> httpCallDao.SubmitXmlToUrl(submitUrl, inXML));
    }

    // Additional test cases can be added for other methods in HttpCallDao
    

   
    @Test
    void callHttpPostWithMap_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        Map<String, String> queryData = new HashMap<>();
        queryData.put("param1", "value1");
        queryData.put("param2", "value2");
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(PostMethod.class))).thenReturn(200);
        when(httpClient.getResponseBodyAsString()).thenReturn("Success Response");

        // Act
        String result = httpCallDao.callHttpPost(urlString, queryData, hcParams);

        // Assert
        assertEquals("Success Response", result);
    }

    @Test
    void callHttpPostWithMap_Failure() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        Map<String, String> queryData = new HashMap<>();
        queryData.put("param1", "value1");
        queryData.put("param2", "value2");
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(PostMethod.class))).thenReturn(500);

        // Act and Assert
        assertThrows(HttpCallException.class, () -> httpCallDao.callHttpPost(urlString, queryData, hcParams));
    }

    @Test
    void callHttpPostWithTimeout_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param1=value1&param2=value2";
        int timeOut = 5000; // 5 seconds
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(PostMethod.class))).thenReturn(200);
        when(httpClient.getResponseBodyAsString()).thenReturn("Success Response");

        // Act
        String result = httpCallDao.callHttpPost(urlString, data, hcParams, timeOut);

        // Assert
        assertEquals("Success Response", result);
    }

    @Test
    void callHttpPostWithTimeout_Failure() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param1=value1&param2=value2";
        int timeOut = 5000; // 5 seconds
        HttpClientParams hcParams = new HttpClientParams();
        when(httpClient.executeMethod(any(PostMethod.class))).thenReturn(500);

        // Act and Assert
        assertThrows(HttpCallException.class, () -> httpCallDao.callHttpPost(urlString, data, hcParams, timeOut));
    }

    // Add more test cases for other methods in HttpCallDao...
}

}
