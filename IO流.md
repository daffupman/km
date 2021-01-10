# IO流

## 一、IO概述

java的输入输出流，即IO流。根据流向，可以分为输入流和输出流。根据数据的类型，可以分为字节流和字符流。IO类的顶级父类为：

|        | 输入流      | 输出流       |
| ------ | ----------- | ------------ |
| 字节流 | InputStream | OutputStream |
| 字符流 | Reader      | Writer       |

## 二、操作文件的File类

File 类是文件和目录路径名的抽象表示，主要用于文件和目录的创建、查找和删除等操作。位于`java.io.`包下。一个File对象表示硬盘中实际存在的一个目录或文件。

无论该路径下是否存在文件或目录，都不影响该File的创建。

### 构造方法

- `public File(String pathname)`
- `public File(String parent, String child)`
- `public FIle(File parent, String child)`

### 常用方法

- `public String getAbsolutePath()`
- `public String getPath()`
- `pulbic String getName()`
- `public long length()`：表示文件的长度，如果是目录，则返回值未指定
- `public boolean exists()`
- `public boolean isDirectory()`
- `public boolean isFile()`
- `public boolean createNewFile()`
- `public boolean delete()`
- `public boolean mkdir()`
- `public boolean mkdirs()`
- `public String[] list()`
- `public File[] listFiles()`

## 三、字节流和字符流

### 字节流

#### OutputStream

字节输出流的所有内的超类，将指定信息写出到目的地

- `public void close()`：先刷新再关闭所占资源
- `public void flush()`
- `public void write(byte[] b)`
- `public void write(byte[] b, int off, int len)`
- `public abstract void write(int b)

#### FileOutputStream

将数据写出到文件

- `public FileOutputStream(File file, boolean append)`

- `public FileOutputStream(String path, boolean append)`

  如果指定的路径下，没有这个文件，那么会自动创建该文件。没有的话，会清空这个文件的数据。如果append为true代表向文件追加数据。

- `write(int b)`：每次写出一个字节数据

- `write(byte[] b)`：写出字节数组

- `write(byte[] b, int off, int len)`

  写出换行

  ```java
  fos.write("\r\n".getBytes());
  ```

  回车（\r）和换行(\n)

  - windows：\r\n
  - unix：\n
  - mac：\r，macos x，与linux统一

#### FileInputStream

文件输入流，从文件中读取字节

构造方法

- `public FileInputStream(File file)`
- `public FileInputStream(String name)`

读取字节数据

- `read()`：读取单个字符，并提升为int类型，读到文件末尾返回-1
- `read(byte[] b)`：将读取的字节放入b中，返回读取的个数

### 字符流

#### Reader

用于读取字符流的所有类的超类，公共方法：

- `public void close()`
- `public int read()`
- `public int read(char[] buf)`

#### FileReader

用于读取字符文件，构造时系统默认的字符编码和默认字节缓冲区

构造

- `public FileReader(File file)`
- `public FileReader(String filename)`
- `public int read()`
- `public int read(char[] buf)`

#### Writer

写出字符流的超类，将指定的走输出到目的地

- `void write(int c)`
- `void write(char[] buf)`
- `abstract void write(char[] cbuf, int off, int len)`
- `void write(String str)`
- `void write(String str, int off, int len)`
- `void flush()`
- `void close()`

#### FileWriter

把字符写到文件里，构造时使用默认的字符编码和默认字节缓冲区

- `public FileWriter(File file)`
- `public FileWriter(String filename)`
- `void write(int b)`
- `void flush()`
- `void close()`
- `void write(char[] buf)`
- `void write(char[] buf, int off, int len)`
- `void write(String str)`
- `void write(String str, int off, int len)`

## 四、缓冲流

缓冲流也称高效流，对4个基本的`FileXxx`的增强，

字节缓冲流：`BufferedInputStream`，`BufferedOutputStream`

字符缓冲流：`BufferedReader`，`BufferedWriter`

构造方法

- `public BufferedInputStream(InputStream in)`
- `public BufferedOutputStream(OutputStream out)`
- `public BufferedReader(Reader in)`
  - `public String readLine()`
  - `public String newLine()`
- `public BufferedWriter(Writer out)`

## 五、转换流

### 字符编码和字符集

字符编码（Character Encoding）

字符集（Charset）：

- ASCII字符集
- GBK字符集
- Unicode字符集

### InputStreamReader

字节流到字符流的桥梁

- `InputStreamReader(InputStream in)`
- `InputStreamReader(InputStream in, String charsetName)`：指定编码字符集

### OutputStreamWriter

字符流到字节流的桥梁

- `OutputStreamWriter(OutputStream in)`
- `OutputStreamWriter(OutputStream in, String charsetName)`

## 六、序列化流

序列化机制：用一个字节序列表示一个对象，该字节序列包含该`对象的数据`、`对象的类型`、`对象中存储的属性`等信息。字节序列写出文件之后，相当于文件中持久保存了对象的信息，这个过程称为正序列化。将对象从文件中读取出来的过程称为反序列化。

如果想让一个类可以序列化，必须要满足以下条件：

- 该类必须实现`java.io.Serializable`接口，同时指定一个序列化版本id，保留公共的无参构造。否则在序列化时，会抛出`NotSerializableException`
- 该类的所有属性都是可序列化的，如果不需要参加序列化，则需要使用`transient`修饰

### ObjectOutputStream

将Java对象写出到文件的对象

- `public ObjectOutputStream(OutputStream out)`
- `public final void writeObject(Object obj)`

### ObjectInputStream

将序列化的数据恢复成对象

- `public ObjectInputStream(InputStream in)`
- `public final Object readObject()`

对于JVM可以反序列化对象，它必须是能够找到class文件的类。如果找不到该类的class文件，则抛出一个
`ClassNotFoundException`异常。

当JVM反序列化对象时，能找到class文件，但是class文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个 InvalidClassException 异常。发生这个异常的原因如下：

- 该类的序列版本号与从流中读取的类描述符的版本号不匹配
- 该类包含未知数据类型
- 该类没有可访问的无参数构造方法

## 七、打印流

用于打印各种数据类型的值。

### PrintStream

- `public PrintStream(String filename)`：使用指定文件名，创建一个新的打印流改变打印流向

  ```java
  PrintStream ps = new PrintStream("ps.txt");
  System.setOut(ps);
  System.out.println(97);// ps.txt文件中存在一个字符A
  ```

  