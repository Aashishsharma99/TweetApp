import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.ConfigurableEnvironment;
import org.springframework.core.env.MutablePropertySources;
import org.springframework.core.env.PropertiesPropertySource;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
class AppPropertiesTest {

    @Autowired
    private AppProperties appProperties;

    @Autowired
    private ConfigurableEnvironment environment;

    @Test
    void getProperty_WithExistingKey_ShouldReturnCorrectValue() {
        // Arrange
        addPropertyToEnvironment("test.property", "testValue");

        // Act
        String result = appProperties.getProperty("test.property");

        // Assert
        assertEquals("testValue", result);
    }

    @Test
    void getProperty_WithNonExistingKey_ShouldReturnNull() {
        // Act
        String result = appProperties.getProperty("non.existing.property");

        // Assert
        assertEquals(null, result);
    }

    @Test
    void getProperty_WithDefaultValue_ExistingKey_ShouldReturnCorrectValue() {
        // Arrange
        addPropertyToEnvironment("test.property", "testValue");

        // Act
        String result = appProperties.getProperty("test.property", "defaultValue");

        // Assert
        assertEquals("testValue", result);
    }

    @Test
    void getProperty_WithDefaultValue_NonExistingKey_ShouldReturnDefaultValue() {
        // Act
        String result = appProperties.getProperty("non.existing.property", "defaultValue");

        // Assert
        assertEquals("defaultValue", result);
    }

    private void addPropertyToEnvironment(String key, String value) {
        MutablePropertySources propertySources = environment.getPropertySources();
        PropertiesPropertySource propertySource = new PropertiesPropertySource("test-source", createProperties(key, value));
        propertySources.addFirst(propertySource);
    }

    private Properties createProperties(String key, String value) {
        Properties properties = new Properties();
        properties.put(key, value);
        return properties;
    }
}
