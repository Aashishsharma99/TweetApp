import com.vzw.cc.valueobjects.TranLog;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class)
public class TranLogTest {

    @InjectMocks
    private TranLog tranLog;

    @Test
    void testPut_whenFieldPositionNegative_thenThrowIndexOutOfBoundsException() {
        assertThrows(IndexOutOfBoundsException.class, () -> tranLog.put("fieldName", "value"));
    }

    @Test
    void testReplace_whenFieldPositionNegative_thenThrowIndexOutOfBoundsException() {
        assertThrows(IndexOutOfBoundsException.class, () -> tranLog.replace("fieldName", "value"));
    }

    @Test
    void testGet_whenFieldPositionNegative_thenThrowIndexOutOfBoundsException() {
        assertThrows(IndexOutOfBoundsException.class, () -> tranLog.get("fieldName"));
    }

    @Test
    void testGetAttributes_whenAttributesExist_thenReturnAttributes() {
        tranLog.attributes = new String[]{"attr1", "attr2"};
        assertArrayEquals(new String[]{"attr1", "attr2"}, tranLog.getAttributes());
    }

    @Test
    void testGetAttributes_whenAttributesNull_thenReturnNull() {
        assertNull(tranLog.getAttributes());
    }

    @Test
    void testGetFields_whenAttributesAndElementsExist_thenReturnAllFields() {
        tranLog.attributes = new String[]{"attr1", "attr2"};
        tranLog.elements = new String[]{"elem1", "elem2"};
        assertArrayEquals(new String[]{"attr1", "attr2", "elem1", "elem2"}, tranLog.getFields());
    }

    @Test
    void testGetFields_whenAttributesAndElementsNull_thenReturnDynamicFields() {
        tranLog.dynamicFields = Arrays.asList("dynField1", "dynField2");
        assertArrayEquals(new String[]{"dynField1", "dynField2"}, tranLog.getFields());
    }

    // Add more test cases for other methods as needed
}
