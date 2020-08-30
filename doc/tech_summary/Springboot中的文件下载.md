#### Springboot实现文件下载

在实现文件下载之前我们先看看Java优雅对文件进行读写的方法。

Java对文件读写的方式有很多，比如基于流的，基于缓冲区的，基于NIO的。各有各的特点,网上对这些方法的测评也有很多，在这里不去比较各个方法的优劣势。我们这里参考基于缓冲区的`BufferedInput/OutputStream`来对文件进行操作。根据网上的相关测评，基于缓冲区的也是最优的读写方式。

读取方式其实很简单，构造器接受一个`FileInputStream`参数，`FileInputStream`接受一个文件路径或者`File`的参数。代码如下：

```java
public static void readFile() throws IOException {
        BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("test.txt"));

        StringBuilder sb = new StringBuilder("");
    	//单次读取的字节数
        byte[] bytes = new byte[1024];
        int total = 0;
        int length = 0;

        while ((length = bufferedInputStream.read(bytes)) != -1 ) {
            sb.append(new String(bytes));
            total += length;
        }

        System.out.println(total);
        System.out.println(sb.toString());
    }
```

写入的方式大同小异，但是有个特别注意的点。基于缓冲区的写需要显示的将缓冲区的内容写到文件中，否则得到的文件将会是空的。

```java
public static void writeFile() throws IOException {
        String s = "key3=value3\n" +
                "key4=value4";

        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(new FileOutputStream("out.txt"));
        bufferedOutputStream.write(s.getBytes());
    
    	//将缓冲区内容写入文件
        bufferedOutputStream.flush();
        System.out.println("ok");
    }
```



文件读写就到这里，接下来是在springboot中如何实现文件下载。

在springboot中我们需要借助到 `ResponseEntity`,接下来的实现就不麻烦了，想要更详细的话参考官方资料。

```
    @GetMapping("/file")
    public ResponseEntity<byte[]> getFile() throws UnsupportedEncodingException {


        byte[] body = null;

        body = "asddfg".getBytes();
        long length = body.length;

        String downloadFileName = URLEncoder.encode("file-download.x", "UTF-8");

        HttpHeaders headers = new HttpHeaders();

        headers.add(HttpHeaders.CONTENT_LENGTH, String.valueOf(length));

        headers.add(HttpHeaders.CONTENT_DISPOSITION, "attachment;filename=" + downloadFileName);

        headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);

        return new ResponseEntity<>(body, headers, HttpStatus.OK);
    }
```

ok，完结。后续遇到什么问题就继续补充吧。

