
## JNI 函数实现

```
115 JNIEXPORT jobjectArray JNICALL
116 Java_org_tensorflow_lite_NativeInterpreterWrapper_getInputNames(JNIEnv* env,
117                                                            jclass clazz,
118                                                            jlong handle) {
119     tflite::Interpreter* interpreter = convertLongToInterpreter(env, handle);
120     if (interpreter == nullptr) return nullptr;
121     jclass string_class = env->FindClass("java/lang/String");
122     if (string_class == nullptr) {
123         throwException(env, kUnsupportedOperationException,
124                    "Internal error: Can not find java/lang/String class to get "
125                    "input names.");
126         return nullptr;
127     }
128     size_t size = interpreter->inputs().size();
129     jobjectArray names = static_cast<jobjectArray>(
130             env->NewObjectArray(size, string_class, env->NewStringUTF("")));
131     for (int i = 0; i < size; ++i) {
132         env->SetObjectArrayElement(names, i,
133                                env->NewStringUTF(interpreter->GetInputName(i)));
134     }
135     return names;
136 }
137 
138 JNIEXPORT void JNICALL
139 Java_org_tensorflow_lite_NativeInterpreterWrapper_allocateTensors(
140         JNIEnv* env, jclass clazz, jlong handle, jlong error_handle) {
141     tflite::Interpreter* interpreter = convertLongToInterpreter(env, handle);
142     if (interpreter == nullptr) return;
143     BufferErrorReporter* error_reporter =
144             convertLongToErrorReporter(env, error_handle);
145     if (error_reporter == nullptr) return;
146 
147     if (interpreter->AllocateTensors() != kTfLiteOk) {
148         throwException(
149                 env, kIllegalStateException,
150                 "Internal error: Unexpected failure when preparing tensor allocations:"
151                 " %s",
152                 error_reporter->CachedErrorMessage());
153     }
154 }
```

代码里的函数定义都是从`javah`中的函数声明中复制过来的，自己手动输入很麻烦。

第116行，定义了`Java_org_tensorflow_lite_NativeInterpreterWrapper_getInputNames`函数，用于获取输入解释器中关联的所有输入数据的名称。

第119行，调用`convertLongToInterpreter`将句柄转换成解释器指针。

第128～133行，获取解释器中的所有输入，并调用`NewObjectArray`构造新的数组对象，然后循环获取设置输入元素的名称，最后返回。

限于篇幅，这里我们只列出了两个函数，更多的函数实现请参阅随书提供的本章代码（位于`ch12/tflite/java/src/main/native/nativeinterpreterwrapper_jni.cc`当中）。

## DataTypes.java

接着编写`DataTypes.java`，用于描述TensorFlow Lite支持的数据类型，和JNI封装实现一一对应。这里其实就是定义了一个枚举类，如代码清单12-16所示。

### 代码清单12-16　`DataTypes.java`

```
 1 package org.tensorflow.lite;
 2 
 3 public enum DataType {
 4   FLOAT32(1),
 5   INT32(2),
 6   UINT8(3),
 7     INT64(4),
 8     STRING(5);
 9 
10     private final int value;
11 
12     DataType(int value) {
13         this.value = value;
14     }
15 
16     public int byteSize() {
17         switch (this) {
18             case FLOAT32:
19                 return 4;
20             case INT32:
21                 return 4;
22             case UINT8:
23                 return 1;
24             case INT64:
25                 return 8;
26             case STRING:
27                 return -1;
28         }
29         throw new IllegalArgumentException(
30                 "DataType error: DataType " + this + " is not supported yet");
31     }
32 
33     int c() {
34         return value;
35     }
36 
37     static DataType fromC(int c) {
38         for (DataType t : values) {
39             if (t.value == c) {
40                 return t;
41             }
42         }
43         throw new IllegalArgumentException(
44                 "DataType error: DataType "
45                         + c
46                         + " is not recognized in Java (version "
47                         + TensorFlowLite.version()
48                         + ")");
49     }
50 
51     String toStringName() {
52         switch (this) {
53             case FLOAT32:
54                 return "float";
55             case INT32:
56                 return "int";
57             case UINT8:
58                 return "byte";
59             case INT64:
60                 return "long";
61             case STRING:
62                 return "string";
63         }
64         throw new IllegalArgumentException(
65                 "DataType error: DataType " + this + " is not supported yet");
66     }
67 
68     private static final DataType[] values = values();
69 }
```

第3行，定义了枚举类型`DataType`，该枚举类型包含了5个值，分别是`FLOAT32`、`INT32`、`UINT8`、`INT64`和`STRING`，分别赋予了1～5个枚举值。

第12行，定义了`DataType`类型的构造方法，可以传入`value`类型初始化内部属性。

第16行，定义了`byteSize()`方法，返回不同类型对应的字节数。如果类型不存在，那么直接抛出异常。

第33行，定义了`c()`方法，直接将内部存储的枚举数字返回。

第37行，定义了`fromC()`方法，会遍历所有的`value`，返回与枚举定义的整型值相同的`value`的值。

第51行，定义了`toStringName()`方法，用于根据当前枚举的值返回枚举对应的描述字符串。如果值不存在，那么直接抛出异常。

## Java Interpreter 类实现

最后在`NativeInterpreterWrapper`的基础上封装顶层Java类、`Interpreter`类，如代码清单12-17所示。

### 代码清单12-17　Java Interpreter类实现
