**Java 输入输出流**

1. Java 中的流分为两种，一种是字节流，另一种是字符流，分别由四个抽象类来表示（每种流包括输入和输出两种所以一共四个）:InputStream，OutputStream，Reader，Writer

2. 字节流 表示以字节为单位从 stream 中读取或往 stream 中写入信息，即 io 包中的 inputstream 类和 outputstream 类的派生类。通常用来读取二进制数据，如图象和声音。

3. 字符流 以 Unicode 字符为导向的 stream，表示以 Unicode 字符为单位从 stream 中读取或往 stream 中写入信息。区别： Reader 和 Writer 要解决的，最主要的问题就是国际化

4. Read 和 Write 并不是取代 InputStream 和 OutputStream，有时，你还必须同时使用"基于 byte 的类"和"基于字符的类"。为此，它还提供了两个"适配器(adapter)"类。InputStreamReader 负责将 InputStream 转化成 Reader，而 OutputStreamWriter 则将 OutputStream 转化成 Writer。