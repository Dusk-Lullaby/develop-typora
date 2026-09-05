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



#### 1.2.2 Ajax 文件上传



### 1.3 文件下载



## 2. Excel 处理

### 2.1 `EasyExcel` 介绍

`EasyExcel` 是阿里巴巴开源的一个 `Excel` 处理框架，以使用简单、节省内存著称。 `EasyExcel` 能大大减少占用内存的主要原因是在解析 Excel 时没有将文件数据一次性全部加载到内存中，而是从磁盘上一行行读取数据，逐个解析。

### 2.2 `EasyExcel`生成 Excel



### 2.3 `EasyExcel` 解析 Excel



### 2.4 `EasyExcel` 导入导出

