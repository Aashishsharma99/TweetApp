package com.vzw.cc.util;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNull;
import static org.mockito.Mockito.any;
import static org.mockito.Mockito.anyInt;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

import java.util.HashMap;

import org.apache.commons.httpclient.params.HttpClientParams;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class HttpCallDaoTest {
    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String)}
     */
    @Test
    void testCallHttpGet() {
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("MakingHttpCall", "https://example.org/example"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, "https://example.org/example"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", XpathValueFinder.LBL_ZERO_ZERO_AMOUNT));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("text/xml", "https://example.org/example"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("MakingHttpCall", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("text/xml", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", new HttpClientParams()));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("MakingHttpCall", "https://example.org/example", new HttpClientParams()));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpGet(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                "https://example.org/example", new HttpClientParams()));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpGet("https://example.org/example",
                XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, new HttpClientParams()));
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpGet2() {

        HttpCallDao.callHttpGet("Making http call", "https://example.org/example");
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, int)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpGet3() {


        HttpCallDao.callHttpGet("Making http call", "https://example.org/example", 1);
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpGet4() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.GetMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHttpGet(HttpCallDao.java:56)
        //   In order to prevent callHttpGet(String, String, HttpClientParams)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHttpGet(String, String, HttpClientParams).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHttpGet("Making http call", "https://example.org/example", new HttpClientParams());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpGet5() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Params may not be null
        //       at org.apache.commons.httpclient.HttpClient.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHttpGet(HttpCallDao.java:55)
        //   In order to prevent callHttpGet(String, String, HttpClientParams)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHttpGet(String, String, HttpClientParams).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", (HttpClientParams) null);
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet6() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter((String) any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(Object.class);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter((String) any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter((String) any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet7() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter((String) any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(null);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter((String) any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter((String) any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet8() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter((String) any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(HttpCallDao.class);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter((String) any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter((String) any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet9() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter((String) any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(Object.class);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter((String) any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("text/xml", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter((String) any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, int)}
     */
    @Test
    void testCallHttpPost() {
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("MakingHttpCall", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("text/xml", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", "https://example.org/example"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("https://example.org/example",
                "callHttpPost: Aborted: xmlreqdoc= is required in data"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("MakingHttpCall", "callHttpPost: Aborted: xmlreqdoc= is required in data"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                "callHttpPost: Aborted: xmlreqdoc= is required in data"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("text/xml", "callHttpPost: Aborted: xmlreqdoc= is required in data"));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("https://example.org/example",
                "callHttpPost: Aborted: xmlreqdoc= is required in data", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("MakingHttpCall", "callHttpPost: Aborted: xmlreqdoc= is required in data", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                "callHttpPost: Aborted: xmlreqdoc= is required in data", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("https://example.org/example",
                "callHttpPost: Aborted: xmlreqdoc= is required in data", -1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("text/xml", "callHttpPost: Aborted: xmlreqdoc= is required in data", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", new HashMap<>()));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("MakingHttpCall", new HashMap<>()));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, new HashMap<>()));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao.callHttpPost("text/xml", new HashMap<>()));
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, int)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpPost2() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:166)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:124)
        //   In order to prevent callHttpPost(String, int)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHttpPost(String, int).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHttpPost("Making http call", 1);
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, String)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpPost3() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:166)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:118)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:86)
        //   In order to prevent callHttpPost(String, String)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHttpPost(String, String).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHttpPost("Making http call", "callHttpPost: Aborted: xmlreqdoc= is required in data");
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, String, int)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpPost4() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:166)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:99)
        //   In order to prevent callHttpPost(String, String, int)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHttpPost(String, String, int).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHttpPost("Making http call", "callHttpPost: Aborted: xmlreqdoc= is required in data", 1);
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, java.util.Map)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHttpPost5() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:166)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:118)
        //       at com.vzw.cc.util.HttpCallDao.callHttpPost(HttpCallDao.java:107)
        //   In order to prevent callHttpPost(String, Map)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHttpPost(String, Map).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHttpPost("Making http call", new HashMap<>());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, java.util.Map)}
     */
    @Test
    void testCallHttpPost6() {
        HashMap<String, String> stringStringMap = new HashMap<>();
        stringStringMap.put("Making http call", "42");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", stringStringMap));
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, java.util.Map)}
     */
    @Test
    void testCallHttpPost7() {
        HashMap<String, String> stringStringMap = new HashMap<>();
        stringStringMap.put("Key", "42");
        stringStringMap.put("Making http call", "42");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", stringStringMap));
    }

    /**
     * Method under test: {@link HttpCallDao#callHTTPBodyPost(String, String, int)}
     */
    @Test
    void testCallHTTPBodyPost() {
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost("https://example.org/example", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost("MakingHttpCall", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost("text/xml", "https://example.org/example", 1));
    }

    /**
     * Method under test: {@link HttpCallDao#callHTTPBodyPost(String, String, int)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testCallHTTPBodyPost2() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Url String': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.callHTTPBodyPost(HttpCallDao.java:134)
        //   In order to prevent callHTTPBodyPost(String, String, int)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   callHTTPBodyPost(String, String, int).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.callHTTPBodyPost("Url String", "https://example.org/example", 1);
    }

    /**
     * Method under test: {@link HttpCallDao#SubmitXmlToUrl(String, String)}
     */
    @Test
    void testSubmitXmlToUrl() {
        assertNull(HttpCallDao.SubmitXmlToUrl("https://example.org/example", "https://example.org/example"));
        assertNull(HttpCallDao.SubmitXmlToUrl("log4jUl", "https://example.org/example"));
    }

    /**
     * Method under test: {@link HttpCallDao#SubmitXmlToUrlWithTimeOut(String, String, int)}
     */
    @Test
    void testSubmitXmlToUrlWithTimeOut() {
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.SubmitXmlToUrlWithTimeOut("https://example.org/example", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.SubmitXmlToUrlWithTimeOut("text/xml", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.SubmitXmlToUrlWithTimeOut("UTF-8", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.SubmitXmlToUrlWithTimeOut(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, "https://example.org/example", 1));
    }

    /**
     * Method under test: {@link HttpCallDao#SubmitXmlToUrlWithTimeOut(String, String, int)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testSubmitXmlToUrlWithTimeOut2() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http post call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.SubmitXmlToUrlWithTimeOut(HttpCallDao.java:289)
        //   In order to prevent SubmitXmlToUrlWithTimeOut(String, String, int)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   SubmitXmlToUrlWithTimeOut(String, String, int).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.SubmitXmlToUrlWithTimeOut("Making http post call", "https://example.org/example", 1);
    }

    /**
     * Method under test: {@link HttpCallDao#SubmitXmlToUrlWithTimeOutAndRetry(String, String, int, int)}
     */
    @Test
    void testSubmitXmlToUrlWithTimeOutAndRetry() {
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao
                .SubmitXmlToUrlWithTimeOutAndRetry("https://example.org/example", "https://example.org/example", 1, 3));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao
                .SubmitXmlToUrlWithTimeOutAndRetry("http.method.retry-handler", "https://example.org/example", 1, 3));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.SubmitXmlToUrlWithTimeOutAndRetry("text/xml", "https://example.org/example", 1, 3));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, HttpCallDao
                .SubmitXmlToUrlWithTimeOutAndRetry(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, "https://example.org/example", 1, 3));
    }

    /**
     * Method under test: {@link HttpCallDao#SubmitXmlToUrlWithTimeOutAndRetry(String, String, int, int)}
     */
    @Test
    @Disabled("TODO: Complete this test")
    void testSubmitXmlToUrlWithTimeOutAndRetry2() {
        // TODO: Complete this test.
        //   Reason: R013 No inputs found that don't throw a trivial exception.
        //   Diffblue Cover tried to run the arrange/act section, but the method under
        //   test threw
        //   java.lang.IllegalArgumentException: Invalid uri 'Making http post call': incorrect path
        //       at org.apache.commons.httpclient.HttpMethodBase.<init>(null)
        //       at org.apache.commons.httpclient.methods.ExpectContinueMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.EntityEnclosingMethod.<init>(null)
        //       at org.apache.commons.httpclient.methods.PostMethod.<init>(null)
        //       at com.vzw.cc.util.HttpCallDao.SubmitXmlToUrlWithTimeOutAndRetry(HttpCallDao.java:319)
        //   In order to prevent SubmitXmlToUrlWithTimeOutAndRetry(String, String, int, int)
        //   from throwing IllegalArgumentException, add constructors or factory
        //   methods that make it easier to construct fully initialized objects used in
        //   SubmitXmlToUrlWithTimeOutAndRetry(String, String, int, int).
        //   See https://diff.blue/R013 to resolve this issue.

        HttpCallDao.SubmitXmlToUrlWithTimeOutAndRetry("Making http post call", "https://example.org/example", 1, 3);
    }
}

////////////////////////////////////////////////////////////////////////////////////////////////////////

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyInt;
import static org.mockito.Mockito.*;

import java.util.HashMap;
import java.util.Map;

import org.apache.commons.httpclient.params.HttpClientParams;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class HttpCallDaoTest {

    // Existing tests...

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet6() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter(any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(Object.class);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter(any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter(any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet7() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter(any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(null);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter(any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter(any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet8() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter(any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(HttpCallDao.class);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter(any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("https://example.org/example", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter(any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpGet(String, String, HttpClientParams)}
     */
    @Test
    void testCallHttpGet9() {
        HttpClientParams httpClientParams = mock(HttpClientParams.class);
        when(httpClientParams.isAuthenticationPreemptive()).thenReturn(true);
        when(httpClientParams.getIntParameter(any(), anyInt())).thenReturn(1);
        when(httpClientParams.getConnectionManagerClass()).thenReturn(Object.class);
        when(httpClientParams.getConnectionManagerTimeout()).thenReturn(1L);
        when(httpClientParams.getParameter(any())).thenReturn("Parameter");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpGet("text/xml", "https://example.org/example", httpClientParams));
        verify(httpClientParams).getConnectionManagerClass();
        verify(httpClientParams).getParameter(any());
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, java.util.Map)}
     */
    @Test
    void testCallHttpPost6() {
        HashMap<String, String> stringStringMap = new HashMap<>();
        stringStringMap.put("Making http call", "42");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", stringStringMap));
    }

    /**
     * Method under test: {@link HttpCallDao#callHttpPost(String, java.util.Map)}
     */
    @Test
    void testCallHttpPost7() {
        HashMap<String, String> stringStringMap = new HashMap<>();
        stringStringMap.put("Key", "42");
        stringStringMap.put("Making http call", "42");
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHttpPost("https://example.org/example", stringStringMap));
    }

    /**
     * Method under test: {@link HttpCallDao#callHTTPBodyPost(String, String, int)}
     */
    @Test
    void testCallHTTPBodyPost() {
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost("https://example.org/example", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost("MakingHttpCall", "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT, "https://example.org/example", 1));
        assertEquals(XpathValueFinder.LBL_ZERO_ZERO_AMOUNT,
                HttpCallDao.callHTTPBodyPost("text/xml", "https://example.org/example", 1));
    }

    /**
     * Method under test: {@link HttpCallDao#callHTTPBodyPost(String, String, int)}
     */
    @Test
    void testCallHTTPBodyPost2() {
        assertThrows(IllegalArgumentException.class,
                () -> HttpCallDao.callHTTPBodyPost("Url String", "https://example.org/example", 1));
    }

    /**
     * Method under test: {@link HttpCallDao#SubmitXmlToUrlWithTimeOutAndRetry(String, String, int, int)}
     */
    @Test
    void testSubmitXmlToUrlWithTimeOutAndRetry2() {
        assertThrows(IllegalArgumentException.class, () -> HttpCallDao
                .SubmitXmlToUrlWithTimeOutAndRetry("Making http post call", "https://example.org/example", 1, 3));
    }
}

