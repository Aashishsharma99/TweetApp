import static org.junit.jupiter.api.Assertions.*;

import java.io.File;

import org.junit.jupiter.api.Test;

class GeneralUtilitiesTest {

    @Test
    void capFirst() {
        assertEquals("Test", GeneralUtilities.capFirst("test"));
        assertEquals("Abc", GeneralUtilities.capFirst("abc"));
        assertEquals("A", GeneralUtilities.capFirst("a"));
        assertNull(GeneralUtilities.capFirst(null));
    }

    @Test
    void qualifiedToSimple() {
        assertEquals("TestClass", GeneralUtilities.qualifiedToSimple("com.example.TestClass"));
        assertEquals("Simple", GeneralUtilities.qualifiedToSimple("Simple"));
        assertNull(GeneralUtilities.qualifiedToSimple(null));
    }

    @Test
    void toJavaClassName() {
        assertEquals("TestClassName", GeneralUtilities.toJavaClassName("test-class-name"));
        assertEquals("AbcDef", GeneralUtilities.toJavaClassName("abc-def"));
        assertEquals("Single", GeneralUtilities.toJavaClassName("single"));
        assertNull(GeneralUtilities.toJavaClassName(null));
    }

    @Test
    void toJavaFieldName() {
        assertEquals("testFieldName", GeneralUtilities.toJavaFieldName("test-field-name"));
        assertEquals("abcDef", GeneralUtilities.toJavaFieldName("abc-def"));
        assertEquals("single", GeneralUtilities.toJavaFieldName("single"));
        assertNull(GeneralUtilities.toJavaFieldName(null));
    }

    @Test
    void toJavaPackage() {
        assertEquals("com.example.testpackage", GeneralUtilities.toJavaPackage("com.example.test-package"));
        assertEquals("com.example.test", GeneralUtilities.toJavaPackage("com.example.test"));
        assertNull(GeneralUtilities.toJavaPackage(null));
    }

    @Test
    void packageToFolder() {
        assertEquals("com\\example\\testpackage", GeneralUtilities.packageToFolder("com.example.testpackage"));
        assertEquals("com\\example\\test", GeneralUtilities.packageToFolder("com.example.test"));
        assertNull(GeneralUtilities.packageToFolder(null));
    }

    @Test
    void toXSDName() {
        assertEquals("xsd:TestClassName", GeneralUtilities.toXSDName("test-class-name"));
        assertEquals("xsd:AbcDef", GeneralUtilities.toXSDName("abc-def"));
        assertEquals("xsd:Single", GeneralUtilities.toXSDName("single"));
        assertNull(GeneralUtilities.toXSDName(null));
    }

    @Test
    void uscoretoHypen() {
        assertEquals("test-class-name", GeneralUtilities.uscoretoHypen("test_class_name"));
        assertEquals("abc-def", GeneralUtilities.uscoretoHypen("abc_def"));
        assertEquals("single", GeneralUtilities.uscoretoHypen("single"));
        assertNull(GeneralUtilities.uscoretoHypen(null));
    }

    @Test
    void hypenToUscore() {
        assertEquals("test_class_name", GeneralUtilities.hypenToUscore("test-class-name"));
        assertEquals("abc_def", GeneralUtilities.hypenToUscore("abc-def"));
        assertEquals("single", GeneralUtilities.hypenToUscore("single"));
        assertNull(GeneralUtilities.hypenToUscore(null));
    }

    @Test
    void removeExt() {
        assertEquals("filename", GeneralUtilities.removeExt("filename.txt"));
        assertEquals("filename", GeneralUtilities.removeExt("filename"));
        assertNull(GeneralUtilities.removeExt(null));
    }

    @Test
    void removeFolder() {
        assertEquals("filename.txt", GeneralUtilities.removeFolder("C:\\path\\to\\filename.txt"));
        assertEquals("filename", GeneralUtilities.removeFolder("filename"));
        assertNull(GeneralUtilities.removeFolder(null));
    }

    @Test
    void folderToPackage() {
        assertEquals("com.example.testpackage", GeneralUtilities.folderToPackage("com\\example\\testpackage"));
        assertEquals("com.example.test", GeneralUtilities.folderToPackage("com\\example\\test"));
        assertNull(GeneralUtilities.folderToPackage(null));
    }

    @Test
    void getFileName() {
        File file = new File("C:\\path\\to\\com\\reap\\test\\TestClass.java");
        assertEquals("com.reap.test.TestClass", GeneralUtilities.getFileName(file));
    }

    @Test
    void getFileNameWithSuffix() {
        File file = new File("C:\\path\\to\\com\\reap\\test\\TestClass.java");
        assertEquals("com.reap.test.suffix.TestClass", GeneralUtilities.getFileName("suffix", "TestClass"));
    }

    @Test
    void getClassName() {
        assertEquals("com.reap.test.TestClass", GeneralUtilities.getClassName(null, "com.reap.test.TestClass"));
        assertEquals("com.reap.test.suffix.TestClass", GeneralUtilities.getClassName("suffix", "com.reap.test.TestClass"));
    }

    @Test
    void getRelativePath() {
        File base = new File("C:\\path\\to");
        File file = new File("C:\\path\\to\\com\\reap\\test\\TestClass.java");
        assertEquals("com/reap/test/TestClass.java", GeneralUtilities.getRelativePath(base, file));
    }

    @Test
    void printStackTrace() {
        Object[] trace = new Object[]{"Class1", "Class2", "Class3"};
        assertEquals("Class1\nClass2\nClass3\n", GeneralUtilities.printStackTrace(trace));
    }
}
