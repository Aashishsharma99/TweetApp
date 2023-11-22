 Object actualMessageListener = ReflectionTestUtils.getField(defaultContainer.getMessageListener());
        assertNotNull(actualMessageListener);
        assertEquals(messageListener, actualMessageListener);
        
        assertEquals(autoStartup, defaultContainer.isAutoStartup());
    }
