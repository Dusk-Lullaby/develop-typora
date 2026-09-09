# 文件上传下载、Excel导入导出

## 1. 文件上传和下载

### 1.1 文件处理的包

`commons-io.jar` 封装了常用的 IO 的相关操作，提供了 `IOUtils` 工具类供开发人员使用

`commons-fileupload.jar` 文件上传的处理包，因为文件上传也会涉及到 IO 操作，因此，该包需要配合 `commons-io.jar` 使用。

*   `FileItemFactory` 文件项工厂，主要提供创建文件项的功能

*   `DiskFileItemFactory` 磁盘文件项工厂，主要用于解析上传文件时，创建对应的文件项
*   `ServletFileUpload Servlet` 文件上传对象，主要用于判断请求是否是文件上传请求，以及请求中的内容解析。解析时需要使用文件项工厂来创建文件项

### 1.2 文件上传

#### 1.2.1 form 表单上传

`index.jsp`

```jsp
<%--
  Created by IntelliJ IDEA.
  User: sonnet
  Date: 2026/9/7
  Time: 20:23
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>文件上传和下载</title>
</head>
<body>
<%--使用form表单进行文件上传的时候，必须要设置enctype属性，--%>
<%--并且这个属性值必须是multipart/form-data--%>
<form action="upload" enctype="multipart/form-data" method="post">
    <input type="text" name="name">
    <input type="file" name="uploadFile">
    <input type="submit" value="上传">
</form>
</body>
</html>
```

`UploadServlet`

```java
package com.sonnet.jsp.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.commons.fileupload2.core.DiskFileItem;
import org.apache.commons.fileupload2.core.DiskFileItemFactory;
import org.apache.commons.fileupload2.core.FileUploadException;
import org.apache.commons.fileupload2.jakarta.servlet6.JakartaServletDiskFileUpload;
import org.apache.commons.fileupload2.jakarta.servlet6.JakartaServletFileUpload;
import org.apache.commons.io.IOUtils;

import java.io.*;
import java.nio.charset.StandardCharsets;
import java.nio.file.Path;
import java.util.List;

@WebServlet("/upload")
public class UploadServlet extends HttpServlet {

    private static final String SAVE_DIR = "D:\\idea_code\\develop\\code\\study\\javaweb-base\\javaweb-base\\java-web06\\upload";

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 判断当前文件是否为文件上传请求
        // 文件上传表单必须使用enctype="multipart/form-data"
        if (JakartaServletFileUpload.isMultipartContent(req)) {
            // 创建磁盘文件项工厂的构建器
            DiskFileItemFactory.Builder builder = DiskFileItemFactory.builder();
            // 设置普通表单字段的默认字符集编码为UTF-8
            // 用于减少中文表单参数出现乱码的情况
            builder.setCharset("UTF-8");
            // 获取Java运行环境提供的系统临时目录
            String tempDirectory = System.getProperty("java.io.tmpdir");
            // 将临时目录的字符串转换为Path对象
            Path repository = Path.of(tempDirectory);
            // 设置上传文件的临时存储目录
            // 当上传内容超过内存缓存区域阈值时，会临时存储到个目录
            builder.setPath(repository);
            // 设置内存缓冲区阈值为4096字节，也就是4MB
            // 文件项不超过4MB时主要保存在内存中
            // 文件项超过4MB时会使用磁盘临时文件
            builder.setThreshold(4096 * 1024);
            // 根据上面设置的字符编码、临时目录和阈值创建文件项工厂
            DiskFileItemFactory factory = builder.get();
            // 使用磁盘文件项工厂创建Jakarta Servlet 6文件上传解析器
            // Tomcat 11需要使用JakartaServletDiskFileUpload
            JakartaServletDiskFileUpload upload = new JakartaServletDiskFileUpload(factory);
            // 设置HTTP上传请求头使用UTF-8编码
            upload.setHeaderCharset(StandardCharsets.UTF_8);
            // 设置每一个上传文件的最大大小为05M
            upload.setMaxFileSize(50 * 1024 * 1024);
            // 设置每次上传的所有文件的总大小为50M
            upload.setMaxSize(50 * 1024 * 1024);
            try {
                // 解析文件上传请求，取得所有表单项
                List<DiskFileItem> fileItems = upload.parseRequest(req);
                // 遍历所有表单项
                for (DiskFileItem fileItem : fileItems) {
                    // 判断当前项目是否为普通表单字段
                    // 例如用户名、描述等普通<input>元素
                    if (fileItem.isFormField()) {
                        // 获取普通表单字段的name属性
                        String fieldName = fileItem.getFieldName();
                        // 获取普通表单字段的值
                        String fieldValue = fileItem.getString(StandardCharsets.UTF_8);
                        System.out.println(fieldName + " => " + fieldValue);
                    } else { // 当前项目是上传的文件
                        File dir = new File(SAVE_DIR);
                        if (!dir.exists()) {
                            dir.mkdirs();
                        }
                        // 创建保存的文件
                        File saveFile = new File(dir, fileItem.getName());
                        // 获取上传文件的输入流
                        InputStream inputStream = fileItem.getInputStream();
                        // 获取上传文件的输出流
                        OutputStream outputStream = new FileOutputStream(saveFile);
                        // 将输入流中的信息拷贝至输出流中，这就是文件保存
                        IOUtils.copy(inputStream, outputStream);
                        // 关闭流
                        IOUtils.closeQuietly(inputStream);
                        IOUtils.closeQuietly(outputStream);
                    }
                }
                resp.setCharacterEncoding("UTF-8");
                resp.setContentType("text/html;charset=UTF-8");
                resp.getWriter().print("上传成功");
            } catch (FileUploadException exception) {
                throw new ServletException("解析文件上传请求失败", exception);
            }
        } else { // 抛出运行时异常
            throw new RuntimeException("请求头中未发现multipart/form-data");
        }
    }
}
```

#### 1.2.2 Ajax 文件上传

```jsp
<%--
  Created by IntelliJ IDEA.
  User: sonnet
  Date: 2026/9/7
  Time: 20:23
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>文件上传和下载</title>
</head>
<body>
<%--使用form表单进行文件上传的时候，必须要设置enctype属性，--%>
<%--并且这个属性值必须是multipart/form-data--%>
<form action="upload" enctype="multipart/form-data" method="post">
    <input type="text" name="name">
    <input type="file" name="uploadFile">
    <input type="submit" value="上传">
</form>

<input type="file" id="uploadFile">
<input type="button" value="上传" id="uploadBtn">
</body>
<script type="text/javascript" src="js/jquery-3.1.1.js"></script>
<script type="text/javascript">
    $(function () {
        $("#uploadBtn").click(function () {
            // 创建一个表单数据，主要用来模拟表单数据
            let formData = new FormData();
            formData.append("file", $("#uploadFile")[0].files[0]);
            formData.append("admin", "admin");
            formData.append("sex", "sex");
            $.ajax({
                url:"upload",
                type:"post",
                data: formData,
                // 告诉jQuery不要处理数据
                processData: false,
                // 告诉jQuery不要设置内容的类型
                contentType: false,
                success: function (resp) {
                    alert(resp);
                },
                error: function (xhr) {

                }
            })
        })
    })
</script>
</html>
```

### 1.3 文件下载

`index.jsp`

```jsp
<%--超链接默认发送请求的方式是get--%>
<a href="download?name=图片.png">图片.png</a>
```

`DownloadServlet`

```java
package com.sonnet.jsp.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.commons.io.IOUtils;

import java.io.*;
import java.nio.charset.StandardCharsets;

@WebServlet("/download")
public class DownloadServlet extends HttpServlet {

    private static final String DOWNLOAD_FILE = "D:\\idea_code\\develop\\code\\study\\javaweb-base\\javaweb-base\\java-web06\\upload";

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 获取下载文件的名字
        String name = req.getParameter("name");
        File file = new File(DOWNLOAD_FILE, name);
        if (file.exists()) {
            // 获取下载的文件名的字节数据
            byte[] data = name.getBytes(StandardCharsets.UTF_8);
            // 转换编码格式，重新构建字符串，因为浏览器默认支持ISO_8859_1
            // 因此，要转换为这种编码下中文才能正常显示
            name = new String(data, StandardCharsets.ISO_8859_1);
            // 设置文件内容的处理方案，以附件的形式处理
            resp.setHeader("Content-Disposition", "attachment;filename=" + name);
            InputStream inputStream =  new FileInputStream(file);
            // 获取响应的输出流，这个流就会将信息输出到页面，从而形成下载的效果
            OutputStream outputStream = resp.getOutputStream();
            // 传输信息
            IOUtils.copy(inputStream,  outputStream);
            IOUtils.closeQuietly(inputStream);
            IOUtils.closeQuietly(outputStream);
        } else {
            resp.setCharacterEncoding("UTF-8");
            resp.setContentType("text/html;charset=UTF-8");
            PrintWriter writer = resp.getWriter();
            writer.print("下载的文件不存在");
            writer.flush();
            writer.close();
        }
    }
}
```

## 2. Excel 处理

### 2.1 `EasyExcel` 介绍

`EasyExcel` 是阿里巴巴开源的一个 `Excel` 处理框架，以使用简单、节省内存著称。 `EasyExcel` 能大大减少占用内存的主要原因是在解析 Excel 时没有将文件数据一次性全部加载到内存中，而是从磁盘上一行行读取数据，逐个解析。

### 2.2 `EasyExcel`生成 Excel



### 2.3 `EasyExcel` 解析 Excel



### 2.4 `EasyExcel` 导入导出

