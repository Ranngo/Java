# 一、Base64转码

## 1.1 原理

主要用于传输8bit字符的编码方式，将每三个8bit的字节转换为4个6bit的字节。

代码如下：

```java
public class base64 {
    public static void main(String[] args) throws UnsupportedEncodingException {
        String encodeStr = Base64.getEncoder().encodeToString("sun".getBytes("utf-8"));
        System.out.println("编码后："+ encodeStr);
        byte[] decodeByte = Base64.getDecoder().decode(encodeStr);
        System.out.println("解码后:"+new String(decodeByte,"utf-8"));

    }
}
```

参考博客：

1. https://blog.csdn.net/s13383754499/article/details/80914157?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control
2. https://blog.csdn.net/wo541075754/article/details/81734770?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162126231116780269830811%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162126231116780269830811&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-1-81734770.pc_search_result_hbase_insert&utm_term=Base64&spm=1018.2226.3001.4187



# 二、POI解析Office文档

通过POI可以对office的常用文档进行解析和读写操作。

## 2.1 HSSF读写xls格式Excel表格



## 2.2 XSSF读写ooxml和xlsx格式文档

## 2.3 HWPF读写word doc格式文档

## 2.4 HSLF读写PowerPoint格式文档

## 2.5 HDGF读写Visio格式文档

## 2.6 HPBF和HSMF读写Publisher和Outlook格式文档

参考博客：

1. https://blog.csdn.net/qq_21137441/article/details/79226171



# 三、FastJson解析Json数据



# 四、commons.io

# 五、Stream流

# 六、Jsoup解析HTML中数据

# 七、阻塞队列

# 八、