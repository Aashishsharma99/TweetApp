import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

import java.util.HashMap;
import java.util.Map;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.HttpMethod;
import org.apache.commons.httpclient.methods.GetMethod;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.params.HttpClientParams;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class HttpCallDaoTest {

    @Test
    public void testCallHttpGetSuccess() throws Exception {
        // Arrange
        HttpClientParams hcParams = new HttpClientParams();
        HttpClient client = mock(HttpClient.class);
        GetMethod mockGetMethod = mock(GetMethod.class);
        when(client.executeMethod(mockGetMethod)).thenReturn(200);
        when(mockGetMethod.getResponseBodyAsString()).thenReturn("Mocked Response");

        // Act
        String result = HttpCallDao.callHttpGet("http://example.com", "param=value", hcParams, client);

        // Assert
        assertEquals("Mocked Response", result);
        verify(client).executeMethod(mockGetMethod);
        verify(mockGetMethod).releaseConnection();
    }

    @Test
    public void testCallHttpGetError() throws Exception {
        // Arrange
        HttpClientParams hcParams = new HttpClientParams();
        HttpClient client = mock(HttpClient.class);
        GetMethod mockGetMethod = mock(GetMethod.class);
        when(client.executeMethod(mockGetMethod)).thenReturn(404);

        // Act
        String result = HttpCallDao.callHttpGet("http://example.com", "param=value", hcParams, client);

        // Assert
        assertEquals("", result);
        verify(client).executeMethod(mockGetMethod);
        verify(mockGetMethod).releaseConnection();
    }

    @Test
    public void testCallHttpGetRedirection() throws Exception {
        // Arrange
        HttpClientParams hcParams = new HttpClientParams();
        HttpClient client = mock(HttpClient.class);
        GetMethod mockGetMethod = mock(GetMethod.class);
        when(client.executeMethod(mockGetMethod)).thenReturn(301);
        when(mockGetMethod.getResponseHeader("location").getValue()).thenReturn("http://redirected.com");
        GetMethod mockRedirectMethod = mock(GetMethod.class);
        when(client.executeMethod(mockRedirectMethod)).thenReturn(200);
        when(mockRedirectMethod.getResponseBodyAsString()).thenReturn("Redirected Response");

        // Act
        String result = HttpCallDao.callHttpGet("http://example.com", "param=value", hcParams, client);

        // Assert
        assertEquals("Redirected Response", result);
        verify(client).executeMethod(mockGetMethod);
        verify(client).executeMethod(mockRedirectMethod);
        verify(mockGetMethod).releaseConnection();
        verify(mockRedirectMethod).releaseConnection();
    }

    @Test
    public void testCallHttpPostSuccess() throws Exception {
        // Arrange
        HttpClientParams hcParams = new HttpClientParams();
        HttpClient client = mock(HttpClient.class);
        PostMethod mockPostMethod = mock(PostMethod.class);
        when(client.executeMethod(mockPostMethod)).thenReturn(200);
        when(mockPostMethod.getResponseBodyAsString()).thenReturn("Mocked Response");

        // Act
        String result = HttpCallDao.callHttpPost("http://example.com", new HashMap<>(), hcParams, client);

        // Assert
        assertEquals("Mocked Response", result);
        verify(client).executeMethod(mockPostMethod);
        verify(mockPostMethod).releaseConnection();
    }

    @Test
    public void testCallHttpPostError() throws Exception {
        // Arrange
        HttpClientParams hcParams = new HttpClientParams();
        HttpClient client = mock(HttpClient.class);
        PostMethod mockPostMethod = mock(PostMethod.class);
        when(client.executeMethod(mockPostMethod)).thenReturn(500);

        // Act
        String result = HttpCallDao.callHttpPost("http://example.com", new HashMap<>(), hcParams, client);

        // Assert
        assertEquals("", result);
        verify(client).executeMethod(mockPostMethod);
        verify(mockPostMethod).releaseConnection();
    }

    @Test
    public void testCallHttpPostWithRequestBody() throws Exception {
        // Arrange
        HttpClientParams hcParams = new HttpClientParams();
        HttpClient client = mock(HttpClient.class);
        PostMethod mockPostMethod = mock(PostMethod.class);
        when(client.executeMethod(mockPostMethod)).thenReturn(200);
        when(mockPostMethod.getResponseBodyAsString()).thenReturn("Mocked Response");

        // Act
        String result = HttpCallDao.callHttpPost("http://example.com", "param=value", hcParams, client);

        // Assert
        assertEquals("Mocked Response", result);
        verify(client).executeMethod(mockPostMethod);
        verify(mockPostMethod).releaseConnection();
    }

    // Add more test cases for different scenarios as needed
}
