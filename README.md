import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

class HttpCallerTest {

    @Mock
    private HttpCallDao httpCallDaoMock;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testMakeCall_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param=value";
        String expectedResponse = "Mocked HTTP response";

        when(httpCallDaoMock.callHttpPost(any(), any())).thenReturn(expectedResponse);

        // Act
        String result = HttpCaller.makeCall(urlString, data);

        // Assert
        assertEquals(expectedResponse, result);
        verify(httpCallDaoMock).callHttpPost(urlString, data);
    }

    @Test
    void testMakeCallOld_Success() throws Exception {
        // Arrange
        String urlString = "http://example.com";
        String data = "param=value";
        String expectedResponse = "Mocked HTTP response";

        // Mock URL and URLConnection, as they are not directly injected
        URL urlMock = mock(URL.class);
        URLConnection urlConnectionMock = mock(URLConnection.class);

        when(urlMock.openConnection()).thenReturn(urlConnectionMock);
        when(urlConnectionMock.getOutputStream()).thenReturn(System.out);

        when(httpCallDaoMock.callHttpPost(any(), any())).thenReturn(expectedResponse);

        // Act
        String result = HttpCaller.makeCallOld(urlString, data);

        // Assert
        assertEquals(expectedResponse, result);
        verify(httpCallDaoMock).callHttpPost(urlString, data);
    }
}
