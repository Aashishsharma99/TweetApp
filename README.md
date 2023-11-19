import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import static org.junit.jupiter.api.Assertions.*;

import java.lang.reflect.Field;
import java.util.List;

import org.junit.jupiter.api.Test;

class UniqueIdGeneratorTest {

    @Test
    void testUniqueIdGenerator() throws NoSuchFieldException, IllegalAccessException {
        // Arrange
        UniqueIdGenerator.setOldTime(0L);
        UniqueIdGenerator.setOffset(0L);

        // Act
        String uniqId = UniqueIdGenerator.getUniqId();
        List<String> uniqIds = UniqueIdGenerator.getUniqIds(5);

        // Assert
        assertNotNull(uniqId);
        assertEquals(5, uniqIds.size());

        // Set values for oldTime and offset using reflection
        setPrivateField(UniqueIdGenerator.class, "oldTime", System.nanoTime());
        setPrivateField(UniqueIdGenerator.class, "offset", 1L);

        // Continue with your test logic
    }

    private void setPrivateField(Class<?> clazz, String fieldName, Object value)
            throws NoSuchFieldException, IllegalAccessException {
        Field field = clazz.getDeclaredField(fieldName);
        field.setAccessible(true);
        field.set(null, value);
    }
}


    @Test
    void testGet21DigitUniqId() {
        // Arrange
        System.setProperty("server.id", "5678");
        UniqueIdGenerator.setOldTime(0L);
        UniqueIdGenerator.setOffset(0L);

        // Act
        String uniqId = UniqueIdGenerator.get21DigitUniqId();

        // Assert
        assertNotNull(uniqId);
        assertTrue(uniqId.matches("\\d{18}567800\\d{2}"));
    }

    @Test
    void testGetUniqIds() {
        // Arrange
        System.setProperty("server.id", "9012");
        UniqueIdGenerator.setOldTime(0L);
        UniqueIdGenerator.setOffset(0L);
        int totalUniqNosNeeded = 3;

        // Act
        List<String> uniqIds = UniqueIdGenerator.getUniqIds(totalUniqNosNeeded);

        // Assert
        assertNotNull(uniqIds);
        assertEquals(totalUniqNosNeeded, uniqIds.size());
        assertTrue(uniqIds.get(0).matches("\\d{17}901200\\d{2}"));
        assertTrue(uniqIds.get(1).matches("\\d{17}901201\\d{2}"));
        assertTrue(uniqIds.get(2).matches("\\d{17}901202\\d{2}"));
    }

    @Test
    void testMainMethod() {
        // Mock System.out to capture the printed output
        System.setOut(new java.io.PrintStream(System.out) {
            private StringBuilder output = new StringBuilder();

            @Override
            public void println(String x) {
                output.append(x).append(System.lineSeparator());
            }

            public String getOutput() {
                return output.toString();
            }
        });

        // Arrange
        System.setProperty("server.id", "3456");

        // Act
        UniqueIdGenerator.main(new String[] {});

        // Assert
        String output = ((java.io.PrintStream) System.out).getOutput();
        assertNotNull(output);
        assertTrue(output.matches("\\d{17}345600\\d{2}" + System.lineSeparator() +
                                 "\\d{17}345601\\d{2}" + System.lineSeparator() +
                                 "\\d{17}345602\\d{2}" + System.lineSeparator() +
                                 "\\d{17}345603\\d{2}" + System.lineSeparator() +
                                 "\\d{17}345604\\d{2}" + System.lineSeparator()));
    }
}
