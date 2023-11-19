package com.vzw.cc.util;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class HttpCallDaoTest {

    @Mock
    private HttpClient httpClient;

    @InjectMocks
    private HttpCallDao httpCallDao;

    @Test
    void testCallHttpGet() throws Exception {
        // Mocking
        when(httpClient.executeMethod(any())).thenReturn(200);
        when(httpClient.getResponseBodyAsString()).thenReturn("Mocked response");

        // Test
        String result = httpCallDao.callHttpGet("http://example.com", "queryData");

        // Verify
        verify(httpClient, times(1)).executeMethod(any());
        verify(httpClient, times(1)).getResponseBodyAsString();
        // Add more verifications based on your requirements

        // Assertions
        // Add assertions based on your requirements
    }

    @Test
    void testCallHttpPost() throws Exception {
        // Mocking
        when(httpClient.executeMethod(any())).thenReturn(200);
        when(httpClient.getResponseBodyAsString()).thenReturn("Mocked response");

        // Test
        String result = httpCallDao.callHttpPost("http://example.com", "data");

        // Verify
        verify(httpClient, times(1)).executeMethod(any());
        verify(httpClient, times(1)).getResponseBodyAsString();
        // Add more verifications based on your requirements

        // Assertions
        // Add assertions based on your requirements
    }

    // Add more test methods for other methods in HttpCallDao
}
