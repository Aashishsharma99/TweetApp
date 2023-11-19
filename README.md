import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.methods.GetMethod;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;
import org.apache.commons.httpclient.params.HttpClientParams;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

class HttpCallDaoTest {

    @Mock
    private HttpClient httpClientMock;

    @Mock
    private GetMethod getMethodMock;

    @Mock
    private PostMethod postMethodMock;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testCallHttpGet_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String queryData = "param=value";
        int timeout = 5000;
        String responseBody = "Mocked HTTP response";

        when(httpClientMock.executeMethod(any())).thenReturn(200);
        when(httpClientMock.getResponseBodyAsString()).thenReturn(responseBody);

        // Act
        String result = HttpCallDao.callHttpGet(urlString, queryData, timeout);

        // Assert
        assertEquals(responseBody, result);
        verify(httpClientMock).executeMethod(any());
    }

    @Test
    void testCallHttpPost_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param=value";
        String responseBody = "Mocked HTTP response";

        when(httpClientMock.executeMethod(any())).thenReturn(200);
        when(httpClientMock.getResponseBodyAsString()).thenReturn(responseBody);

        // Act
        String result = HttpCallDao.callHttpPost(urlString, data);

        // Assert
        assertEquals(responseBody, result);
        verify(httpClientMock).executeMethod(any());
    }

    @Test
    void testCallHttpPostWithMap_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        StringRequestEntity stringRequestEntity = new StringRequestEntity("param=value", "text/plain", "UTF-8");
        String responseBody = "Mocked HTTP response";

        when(httpClientMock.executeMethod(any())).thenReturn(200);
        when(httpClientMock.getResponseBodyAsString()).thenReturn(responseBody);

        // Act
        String result = HttpCallDao.callHTTPBodyPost(urlString, stringRequestEntity, 5000);

        // Assert
        assertEquals(responseBody, result);
        verify(httpClientMock).executeMethod(any());
    }
}
