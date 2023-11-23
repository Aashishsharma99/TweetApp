import com.vzw.cc.valueobjects.SDO;
import org.junit.jupiter.api.Test;
import java.util.Arrays;
import java.util.List;
import static org.junit.jupiter.api.Assertions.*;

public class SDOTest {

    @Test
    void compareToMethodShouldCompareNames() {
        SDO sdo1 = new SDO();
        SDO sdo2 = new SDO();
        sdo1.setName("abc");
        sdo2.setName("def");

        assertTrue(sdo1.compareTo(sdo2) < 0);
    }

    @Test
    void equalsMethodShouldReturnTrueForEqualObjects() {
        SDO sdo1 = new SDO();
        SDO sdo2 = new SDO();
        sdo1.setName("abc");
        sdo2.setName("abc");

        assertTrue(sdo1.equals(sdo2));
    }

    @Test
    void equalsMethodShouldReturnFalseForDifferentObjects() {
        SDO sdo1 = new SDO();
        SDO sdo2 = new SDO();
        sdo1.setName("abc");
        sdo2.setName("def");

        assertFalse(sdo1.equals(sdo2));
    }

    @Test
    void putMethodShouldHandleDynamicFields() {
        SDO sdo = new SDO();
        String dynamicField = "dynamicField";
        Object value = "dynamicValue";

        sdo.put(dynamicField, value);

        assertEquals(value, sdo.get(dynamicField));
        assertTrue(sdo.getAttributes() == null && sdo.getElements() == null && sdo.isDynamic());
    }

    @Test
    void replaceMethodShouldReplaceExistingValue() {
        SDO sdo = new SDO();
        String fieldName = "field";
        Object originalValue = "originalValue";
        Object newValue = "newValue";

        sdo.put(fieldName, originalValue);
        sdo.replace(fieldName, newValue);

        assertEquals(newValue, sdo.get(fieldName));
    }

    @Test
    void getFieldsMethodShouldIncludeAttributesAndElements() {
        SDO sdo = new SDO();
        sdo.attributes = new String[]{"attr1", "attr2"};
        sdo.elements = new String[]{"elem1", "elem2"};

        String[] fields = sdo.getFields();

        assertTrue(Arrays.asList(fields).containsAll(List.of("attr1", "attr2", "elem1", "elem2")));
    }

    @Test
    void putMethodShouldHandleNullValue() {
        SDO sdo = new SDO();
        String fieldName = "field";

        sdo.put(fieldName, null);

        assertNull(sdo.get(fieldName));
    }

    @Test
    void putMethodShouldHandleListValue() {
        SDO sdo = new SDO();
        String fieldName = "field";
        List<String> value = Arrays.asList("value1", "value2");

        sdo.put(fieldName, value);

        assertEquals(value, sdo.get(fieldName));
    }

    @Test
    void getFieldPosMethodShouldReturnCorrectPositionForAttribute() {
        SDO sdo = new SDO();
        sdo.attributes = new String[]{"attr1", "attr2"};
        String fieldName = "attr2";

        int position = sdo.getFieldPos(fieldName);

        assertEquals(1, position);
    }

    @Test
    void getFieldPosMethodShouldReturnCorrectPositionForElement() {
        SDO sdo = new SDO();
        sdo.elements = new String[]{"elem1", "elem2"};
        String fieldName = "elem2";

        int position = sdo.getFieldPos(fieldName);

        assertEquals(1, position);
    }

    @Test
    void getFieldPosMethodShouldReturnCorrectPositionForDynamicField() {
        SDO sdo = new SDO();
        sdo.dynamicFields = Arrays.asList("dyn1", "dyn2");
        String fieldName = "dyn2";

        int position = sdo.getFieldPos(fieldName);

        assertEquals(1, position);
    }

    @Test
    void getFieldPosMethodShouldReturnNegativeForNonExistentField() {
        SDO sdo = new SDO();
        sdo.attributes = new String[]{"attr1", "attr2"};
        sdo.elements = new String[]{"elem1", "elem2"};
        sdo.dynamicFields = Arrays.asList("dyn1", "dyn2");
        String fieldName = "nonExistent";

        int position = sdo.getFieldPos(fieldName);

        assertEquals(-1, position);
    }

    // Add more test cases for other methods and edge cases as needed
}
