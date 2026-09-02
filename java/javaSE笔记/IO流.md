# I / O流

## File类

需要导包`import java.io.File`



### 1. File类的作用

`java.io.File`类是对存储在磁盘上的文件信息的一个抽象表示，主要用于文件的创建，查找和删除.



### 2. File类的使用

#### 常用构造方法：

```java
public File(String pathname);	//通过将给定的字符串路径名转换为抽象路径来创建File实例

piblic File(String parent, String child);	//通过给定的字符父级串路径和字符串子级路径来创建File实例

public File(File parent, String child);	//通过父级抽象路径名和字符串子路径创建File实例
```



示例：

```java
package com.Sonnet.File;

import java.io.File;

public class Example1 {
    public static void main(String[] args) {
        File file1 = new File("D:\\idea_code\\develop\\code\\test\\javaSE-01\\chapter17\\File - test\\file - test.txt");
        File file2 = new File("D:\\idea_code\\develop\\code\\test\\javaSE-01\\chapter17\\File - test", "file - test.txt");
        File parent = new File("D:\\idea_code\\develop\\code\\test\\javaSE-01\\chapter17\\File - test");
        File file3 = new File(parent, "file - test.txt");
    }
}

```



#### 常用使用方法：

```java
public String getAbsolutePath();	//获取文件的绝对路径

public String getName();	//获取文件的名字

public String getPath();	//获取文件的路径

public File getParentFile();	//获取文件的父文件

public String getParent();	//获取文件的父文件路径

public long length();	//获取文件的大小

public long lastModified();	//获取文件的最后修改时间
```

> 绝对路径：带有盘符的叫做绝对路径
>
> 相对路径：不带盘符的叫做相对路径，相对路径是先对于当前工程来定位的



示例：

```java
package com.Sonnet.File;

import java.io.File;

public class Example2 {
    public static void main(String[] args) {
        File file = new File("D:\\idea_code\\develop\\code\\test\\javaSE-01\\chapter17\\File - test\\file - test.txt");
        //获取文件的绝对路径
        String absPath = file.getAbsolutePath();
        System.out.println(absPath);
        //获取文件的路径
        //可能是相对路径
        //也可能是绝对路径
        //根据构造方法的定位
        String path = file.getPath();
        System.out.println(path);
        //获取文件的大小,单位是字节
        long length = file.length();
        System.out.println(length);
        //获取文件的最后修改时间,单位毫秒
        long lastUpdateTime = file.lastModified();
        System.out.println(lastUpdateTime);

        System.out.println();
        //获取文件的名字
        System.out.println(file.getName());
        //获取文件的父级文件夹对象
        File parentFile = file.getParentFile();
        System.out.println(parentFile.getPath());
        System.out.println(parentFile.getName());
        //父级文件路径
        String parentPath = file.getParent();
        System.out.println(parentPath);



        System.out.println();
        //相对路径
        File file1 = new File("chapter17\\src\\FileExample.txt");
        System.out.println(file1.getAbsolutePath());
        System.out.println(file1.getPath());
        System.out.println(file1.getName());
        System.out.println(file1.length());

        System.out.println();
        //父级
        File fileParent1 = file1.getParentFile();
        System.out.println(fileParent1.getPath());
        System.out.println(fileParent1.getName());


        // 添加检查
        if (file1.exists()) {
            System.out.println("文件存在");
            if (file1.isFile()) {
                System.out.println("是普通文件");
                System.out.println("文件大小: " + file1.length() + " 字节");
            } else {
                System.out.println("这不是文件，可能是目录");
            }
        } else {
            System.out.println("文件不存在！路径: " + file1.getAbsolutePath());
        }
    }
}

```

```tex
D:\idea_code\develop\code\test\javaSE-01\chapter17\File - test\file - test.txt
D:\idea_code\develop\code\test\javaSE-01\chapter17\File - test\file - test.txt
14
1766912673008

file - test.txt
D:\idea_code\develop\code\test\javaSE-01\chapter17\File - test
File - test
D:\idea_code\develop\code\test\javaSE-01\chapter17\File - test

D:\idea_code\develop\code\test\javaSE-01\chapter17\src\FileExample.txt
chapter17\src\FileExample.txt
FileExample.txt
14

chapter17\src
src
文件存在
是普通文件
文件大小: 14 字节

```



#### 文件的相关判断：

```java
public boolean canRead();	//是否可读

public boolean canWrite();	//是否可写

public boolean exists();	//是否存在

public boolean isDirectory();	//是否是目录

public boolean isFile();	//是否是一个正常的文件

public boolean isHidden();	//是否隐藏

public boolean canExecute();	//是否可执行

public boolean createNewFile() throw IOException;	//创建新的文件

public boolean delete();	//删除文件

public boolean mkdir();	//创建目录，一级

public boolean mkdirs();	//创建目录， 多级

public boolean renameTo(File dest);	//文件重命名
```



示例：

```java
package com.Sonnet.File;

import java.io.File;
import java.io.IOException;

public class Example3 {
    public static void main(String[] args) {
        File file1 = new File("chapter17\\File - test\\recode\\recode.txt");
        //判断是否可读
        boolean readable = file1.canRead();
        System.out.println("文件是否可读：" + readable);
        //判断是否可写
        boolean writable = file1.canWrite();
        System.out.println("文件是否可写：" + writable);
        //判断文件是否存在
        boolean exists = file1.exists();
        System.out.println("文件是否存在：" + exists);
        System.out.println("乱写的文件是否存在：" + new File("夹心饼干乱写的").exists());
        //判断文件是否是目录
        boolean isDirectory = file1.isDirectory();
        System.out.println("文件是否是目录:" + isDirectory);
        System.out.println("父级文件是否是目录：" + file1.getParentFile().isDirectory());
        //判断是否是一个正常文件
        boolean isFile = file1.isFile();
        System.out.println("文件是否是一个正常文件:" + isFile);
        //判断文件是否隐藏
        boolean isHidden = file1.isHidden();
        System.out.println("文件是否隐藏：" + isHidden);
        //判断文件是否可执行
        //可执行是指双击之后可执行
        boolean executable = file1.canExecute();
        System.out.println("判断文件是否可执行：" + executable);
        //判断是否创建文件
        File file2 = new File("chapter17\\File - test\\recode\\createNewFile.txt");
        if (!file2.exists()) {
            try {
                //创建文件时必须保证该文件的父级目录存在，否则报IO异常
                boolean success = file2.createNewFile();
                System.out.println("文件创建是否成功" + success);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        //创建一级目录
        File file3 = new File("chapter17\\FileTest\\test.txt");
        File parentFile = file3.getParentFile();
        //通常会和创建目录的方法配合使用
        if (!parentFile.exists()) {
            //创建父级目录，但只能创建一级
            boolean creatMkdir = parentFile.mkdir();
            System.out.println("父级目录是否创建：" + creatMkdir);
        }
        if (!file3.exists()) {
            try {
                boolean creatSuccess = file3.createNewFile();
                System.out.println("文件是否创建成功：" + creatSuccess);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        //创建多级目录
        File file4 = new File("chapter17\\Mkdir2\\Mkdir3\\Mkdir4\\test.txt");
        File parentFile4 = file4.getParentFile();
        if (!parentFile4.exists()) {
            boolean creatMkdir = parentFile4.mkdirs();
            System.out.println("多级目录是否创建：" + creatMkdir);
        }
        if (!file4.exists()) {
            try {
                boolean newFile = file4.createNewFile();
                System.out.println("文件是否创建" + newFile);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        //判断文件是否删除
        boolean deleteSuccess = file4.delete();
        System.out.println("文件是否删除:" + deleteSuccess);
        //删除文件夹时，必须保证问价夹中没有任何文件,也就是保证文件夹是空的
        boolean deleteFolders = file4.getParentFile().getParentFile().delete();
        System.out.println("文件夹是否删除：" + deleteFolders);

        //判断文件是否重命名
        File renameDest = new File("chapter17\\File - test\\recode——new\\recode.txt");
        //文件重命名是目标文件夹时，必须保证目标文件夹存在，重命名操作成功后，原来的文件就移动了
        boolean rename = file1.renameTo(renameDest);
        System.out.println("文件重命名是否成功：" + rename);

        //判断文件是否重命名
        File renameDest2 = new File("chapter17\\File - test\\recode\\夹心饼干.txt");
        //文件重命名是目标文件夹时，必须保证目标文件夹存在，重命名操作成功后，原来的文件就移动了
        boolean rename2 = file1.renameTo(renameDest2);
        System.out.println("文件重命名是否成功：" + rename2);
    }
}

```

```tex
文件是否可读：true
文件是否可写：false
文件是否存在：true
乱写的文件是否存在：false
文件是否是目录:false
父级文件是否是目录：true
文件是否是一个正常文件:true
文件是否隐藏：true
判断文件是否可执行：true
文件是否创建true
文件是否删除:true
文件夹是否删除：false
文件重命名是否成功：false
文件重命名是否成功：true
```

**删除文件夹时必须保证，文件夹为空，否则删除失败**



#### 文件列表相关：

```java
public File[] listFiles();	//列出文件夹下所有文件

public File[] listFiles(FileFilter filter);	//列出文件夹下所有满足条件的文件
```



示例：

```java
package com.Sonnet.File;

import java.io.File;
import java.io.FileFilter;

public class Example4 {
    public static void main(String[] args) {


        File directory = new File("D:\\Typora\\develop\\java\\javaSE笔记");
        //列出文件夹中所有文件
        File[] files = directory.listFiles();
        if (files != null) {
//            for (int i = 0; i < files.length; i++) {
//                File file = files[i];
//            }
            //增强for循环
            for (File file : files) {
                System.out.println(file.getPath());
            }
        }

        File[] listFiles = directory.listFiles(new FileFilter() {
            //表示接收文件的条件
            @Override
            public boolean accept(File file) {
                //获取文件名，包含后缀
                String name = file.getName();
                //返回文件名.md结尾的文件
                return name.endsWith(".md");
            }
        });

        if (listFiles != null) {
            for (File file : listFiles) {
                System.out.println(file.getPath());
            }
        }

    }
}

```

```tex
D:\Typora\develop\java\javaSE笔记\final.md
D:\Typora\develop\java\javaSE笔记\IO流.md
D:\Typora\develop\java\javaSE笔记\photo
D:\Typora\develop\java\javaSE笔记\内部类.md
D:\Typora\develop\java\javaSE笔记\多态和Object类.md
D:\Typora\develop\java\javaSE笔记\字符串.md
D:\Typora\develop\java\javaSE笔记\常见API.md
D:\Typora\develop\java\javaSE笔记\异常.md
D:\Typora\develop\java\javaSE笔记\抽象类和抽象方法.md
D:\Typora\develop\java\javaSE笔记\接口.md
D:\Typora\develop\java\javaSE笔记\权限修饰符和代码块.md
D:\Typora\develop\java\javaSE笔记\final.md
D:\Typora\develop\java\javaSE笔记\IO流.md
D:\Typora\develop\java\javaSE笔记\内部类.md
D:\Typora\develop\java\javaSE笔记\多态和Object类.md
D:\Typora\develop\java\javaSE笔记\字符串.md
D:\Typora\develop\java\javaSE笔记\常见API.md
D:\Typora\develop\java\javaSE笔记\异常.md
D:\Typora\develop\java\javaSE笔记\抽象类和抽象方法.md
D:\Typora\develop\java\javaSE笔记\接口.md
D:\Typora\develop\java\javaSE笔记\权限修饰符和代码块.md

Process finished with exit code 0

```



> 增强for循环：
>
> ```java
> 语法：
> for (元素类型 变量名 : 数组或集合) {
>     // 使用变量名操作当前元素
> }
> 
> 示例：
> for (File file : files) {
>     sout("file.getPath()");
> }
> ```
>
> 增强for循环的语法更加简洁，适合用在遍历场景，但增强for循环有局限性，在需要索引或者修改集合结构时，回退到传统for循环即可



### 3. 递归

在方法内部再调用自身就是递归，递归分为直接递归和间接递归。

直接递归就是方法自己调用自己。

间接递归就是多个方法之间相互调用，形成一个闭环，从而构成递归。

**使用递归的时候必须要有出口，也就是让递归停下来，否则将导致栈内存溢出。**



示例一：

使用递归求1 - 100的累加和

```java
package com.Sonnet.File;

/**
 * 递归求和
 */
public class Example5 {
    public static void main(String[] args) {
        System.out.println(addSum(100));

    }

    public static int addSum(int n) {
        if (n == 1) return 1;
        return n + addSum(--n);
    }
}
```

> addSum(5) = 5 + addSum(4)
>
> addSum(4) = 4 + addSum(3)
>
> addSum(3) = 3 + addSum(2)
>
> addSum(2) = 2 + addSum(1)
>
> addSum(1) = 1
>
> 综上：addSum(5) = 5 + 4 +3 +2 +1



示例二：

递归求6的阶乘

```java
package com.Sonnet.File;

public class Example7 {
    public static void main(String[] args) {
        System.out.println(Steps(6));
    }

    public static int Steps(int n) {
        if (n == 1 || n == 0) return 1;
        return n * Steps(n - 1);
    }
}

```



示例三：

使用递归打印文件夹下的所有文件信息

```java
package com.Sonnet.File;

import java.io.File;

public class Example9 {
    public static void main(String[] args) {
        File folder = new File("D:\\Typora");
        recursiveFile(folder);

    }

    public static void recursiveFile(File folder) {
        //检测是否是文件夹
        if (folder.isDirectory()) {
            File[] files = folder.listFiles();
            if (files != null) {
                for (File file : files) {
                    //是文件夹就调用方法
                    recursiveFile(file);
                }
            }
        //不是文件夹就打印文件的路径
        } else {
            System.out.println(folder.getPath());
        }
    }

}

```



示例四：

删除一个文件夹

```java
package com.Sonnet.File;

import java.io.File;

public class Example10 {
    public static void main(String[] args) {
        deleteFolder(new File("chapter17\\Steps-test"));
    }

    public static void deleteFolder(File folder) {
        //检测是否是文件夹，是文件夹就进去看
        if (folder.isDirectory()) {
            File[] files = folder.listFiles();
            //如果里面内容为空，就直接删除
            if (files == null) {
                folder.delete();
            //如果不为空，就调用方法
            } else {
                for (File file : files) {
                    deleteFolder(file);
                }
                //文件夹中文件删除完毕之后，文件夹也需要删除
                folder.delete();
            }
        //不是文件夹就删除
        } else {
            folder.delete();
        }
    }
}


```

> 注意：使用程序删除的文件不会在回收站中出现，使用时需小心



## I / O 流

I / O 是input和output两个单词的首字母，表示输入和输出，其参照物就是内存，写入内存就是输入，从内存中读取出来就是输出。



Java中的I / O流：

磁盘和内存是两个不用的设备，它们之间要实现数据的交互，就必须建立一条通道，在Java中实现建立这样的通道的是I / O流。Java中的I / O流是按照数据类型来划分的，分别是字节流（缓冲流、二进制数据流和对象流）、字符流。



### 1. 字节流

程序使用字节流执行8位字节的输入和输出，所有的字节流类均来自`InputStream`和`OutputStream`。



#### OutputStream常用方法：

```java
public abstract void write(int b) throw IOException;	//写一个字节

public void write(byte b[]) throws IOException;	//将给定的字节数组内容全部写入文件中

public void write(byte b[], int off, int len) throws IOException;	//将给定的字节数组中指定的便宜量和长度之间的内容写入文件中

public void flush() throws IOException;	//强制将通道中数据全部写出

public void close() throws IOException;	//关闭通道    
```

#### 文件输出流FileOutputStream的构造方法：

```java
public FileOutputStream(String name) throws FileNotFoundException;	//根据提供的文件路径构建一条文件输出通道

public FileOutputStream(String name, boolean append) throws FileNotFoundException;	//根据提供的文件路径构建一条文件输出通道，并根据append的值决定是将内容追加到末尾还是直接覆盖

public FileOutputStream(File file) throws FileNotFoundException;	//根据提供的文件信息构建一条文件输出通道

public FileOutputStream(File file, boolean append) throws FileNotFoundException;	//根据提供的文件信息构建一条文件输出通道，并根据append的值决定是将内容追加到末尾还是直接覆盖
```



示例：

使用文件输出流将“夹心饼干真好吃”写入磁盘文件中

```java
package com.Sonnet.IOStream;

import java.io.*;

public class Example1 {
    public static void main(String[] args) {
        try {
            File dir = new File("chapter17\\IO_Test");
            //如果文件夹不存在就构建多级目录
            if (!dir.exists()) dir.mkdirs();
            //将内容写入文件时，需保证这个文件的父级目录一定存在，否则会报文件未找到异常
            File file = new File("chapter17\\IO_Test\\io.txt");
            //构建磁盘文件与内存的通道
            //true 是追加，false是覆盖，默认为覆盖
            OutputStream os = new FileOutputStream(file, true);
            String text = "夹心饼干真好吃";
            byte[] bytes1 = text.getBytes();
            byte[] bytes2 = "嫁衣".getBytes();
            //一次向通道写一个字节至文件中
            for (byte b : bytes1) {
                os.write(b);
            }
            //向通道中一次将所有字节数组中的内容全部输出过去
            os.write(bytes2);
            //使用偏移量和长度的时候需要考虑数组下标越界
            os.write(bytes1, 3, bytes1.length - 4);
            //在通道关闭之前使用，强制将通道中的数据输出到文件中
            os.flush();
            //关闭通道
            os.close();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```



#### InputStream常用方法:

```java
public abstract int read() throws IOException;	//读取一个字节

public int read(byte b[]) throws IOException;	//读取多个字节存储至给定的字节数组中

public int read(byte b[], int off, int len) throws IOException;		//读取多个字节按照给定的偏移量及长度存储在给定的字节数组中

public void close() throws IOException;	//关闭流，也就是关闭磁盘和内存之间的通道

public int available() throws IOExcption;	//获取通道中数据的长度
```

#### 文件输入流FileInputStream 构造方法:

```java
public FileInputStream(String name) throws FileNotFoundException;	//根据提供的文件路径构建一条文件输入通道

public FileInputStream(File file) throws FileNotFoundException;	//根据提供的文件信息构建一条文件输入通道
```



示例一：

使用文件输入流将文件信息从磁盘中读取到内存中来，并在控制台输出。

```java
package com.Sonnet.IOStream;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;

public class Example2 {
    public static void main(String[] args) {
        try {
            InputStream is = new FileInputStream("chapter17\\IO_Test\\io.txt");
            //获取通道中的数据长度
            int length = is.available();
            //根据通道中数据的长度，构建字节数组，但需要考虑到一点，如果通道中数据长度过长，那么字节数组构建太大，则可能导致内存不够，比如使用流读取一个大小为10G的文件
            byte[] buffer = new byte[length];
            byte[] buffer1 = new byte[length];
            int index = 0;
            //读取通道中的数据，一次读取一个字节。如果读取到末尾，则返回-1
            while (true) {
                byte b = (byte)is.read();
                if (b == -1) break;
                buffer[index++] = b;
            }
            System.out.println(new String(buffer));

            InputStream is2 = new FileInputStream("chapter17\\IO_Test\\io.txt");
            //将通道中的数据全部读取到buffer1数组中
            int readCount = is2.read(buffer1);
            System.out.println("读取了" + readCount + "个字节");
            System.out.println(new String(buffer1));
            //关闭通道
            is.close();
            is2.close();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```

> 如果通道中数据过长，那么根据通道中数据的长度来构建字节数组，则可能导致内存不够，比如使用流读取一个大小为10G的内存，那么通道中就应该存在10G长的数据，此时应该怎么办？
>
> ```java
> package com.Sonnet.IOStream;
> 
> import java.io.FileInputStream;
> import java.io.FileNotFoundException;
> import java.io.IOException;
> import java.io.InputStream;
> 
> public class Example3 {
>     public static void main(String[] args) {
>         try {
>             InputStream is = new FileInputStream("chapter17\\IO_Test\\io.txt");
>             //构建了一个长度为31的字节数组
>             //实际开发中字节数组一般定义为1024的整数倍
>             byte[] buffer = new byte[31];
>             while (true) {
>                 //从通道中读取数据存入字节数组buffer中，返回值就是读取的字节长度
>                 int len = is.read(buffer);
>                 //如果读取到数据的末尾则返回-1
>                 if (len == -1) break;
>                 System.out.println(len);
>                 System.out.println(new String(buffer));
>             }
>             is.close();
>         } catch (FileNotFoundException e) {
>             throw new RuntimeException(e);
>         } catch (IOException e) {
>             throw new RuntimeException(e);
>         }
>     }
> }
> 
> ```



示例二：

```java
package com.Sonnet.IOStream;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;

public class Example5 {
    public static void main(String[] args) {
        try {
            InputStream is = new FileInputStream("chapter17\\IO_Test\\io.txt");
            byte[] buffer = new byte[1024];
            int offset = 0;
            while (true) {
                int len = is.read(buffer, offset, 40 );
                if (len == -1) break;
                System.out.println(len);
                offset += len;
            }
            System.out.println(new String(buffer, 0, offset));
            is.close();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

    }
}

```



练习：使用字节流实现磁盘文件的拷贝

```java
package com.Sonnet.IOStream;

import java.io.*;

public class Example7 {
    public static void main(String[] args) {
        String sourceFile = "chapter17\\IO_Test\\io.txt";
        String destFile = "chapter17\\IO_Test\\IO_OutputStream.txt";
        new File("chapter17\\IO_Test\\IO_OutputStream.txt").delete();
        copyFile1(sourceFile, destFile);
    }

    /**
     * 文件拷贝
     *
     * @param sourceFile
     * @param destFile
     */
    public static void copyFile1(String sourceFile, String destFile) {
        File file = new File(destFile);
        File parent = file.getParentFile();
        if (!parent.exists()) {
            parent.mkdirs();
        }
        InputStream is = null;
        OutputStream os = null;
        try {
            is = new FileInputStream(sourceFile);
            os = new FileOutputStream(destFile);
            byte[] buffer = new byte[4096];
            while (true) {
                int len = is.read(buffer);
                if (len == -1) break;
                os.write(buffer, 0, len);
            }
            os.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            close(is, os);
        }
    }

        public static void copyFile2 (String sourceFile, String destFile) {
            File file = new File(destFile);
            File parent = file.getParentFile();
            if (!parent.exists()) {
                parent.mkdirs();
            }
            //jdk1.7之后可以使用
            //try(){}catch{}语句,使用该语句无需管理流的close，可以节省许多代码
            //写在括号中的代码只能够是实现了AutoCloseable接口的类
            //InputSteam和OutputSteam继承了Closeable接口
            //Closeable继承了AutoCloseable接口
            try (InputStream is = new FileInputStream(sourceFile);
                 OutputStream os = new FileOutputStream(destFile)) {
                byte[] buffer = new byte[4096];
                while (true) {
                    int len = is.read(buffer);
                    if (len == -1) break;
                    os.write(buffer, 0, len);
                }
                os.flush();
            } catch (FileNotFoundException e) {
                throw new RuntimeException(e);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }


    //不定长自变量,本质是一个数组，在使用不定长自变量作为方法时，该方法必须为该方法的最后一个参数
    public static void close (Closeable...closeables){
        for (Closeable c : closeables) {
            if (c != null) {
                try {
                    c.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}

```

> 注意：
>
> `try(){}catch{}`语句，仅限JDK1.7以后之后使用
>
> 使用该语句可以无需管理流通道的close，可以节省许多代码
>
> 但是写在括号中的内容只能够是**实现了`AutoCloseable`接口的类**
>
> 在该实例中，`InputStream`和`OutputStream`继承了`Closeable`接口
>
> 而`Closeable`继承了``AutoCloseable接口



#### 应用场景：

字节流是一种低级的流，读写数据经常产生乱码

因此，字节流通常适用于读写原始数据，也就是基本数据类型



### 2. 字符流

> 在使用字节流的时候经常会出现乱码，说明字节流存在弊端，在读取文件的时候，可以使用字符流
>
> Java平台使用Unicode约定存储字符值。字符流I / O会自动将此内部格式与本地字符集转换。在西方语言环境中，本地字符集通常是ASCII的8位超集



```tex
              Writer (抽象类)
                 ↑
     ------------------------
     |                      |
FileWriter           OutputStreamWriter
                         ↑
                -------------------
                |                 |
           StringWriter     BufferedWriter
```



#### Writer常用方法：

```java
public void writer(int c) throws IOException;	//写一个字符

public void writer(char cbuf[]) throws IOException;	//将给定的字符数组内容写入到文件中

abstract public void writer(char cbuf[], int off, int len);	//将给定的字符数组中给定的偏移量和长度的内容写入到文件中

public void writer(String str) throws IOException;	//将字符串写入到文件中

abstract public void flush() throws IOException;	//强制将通道中的数据全部写出

abstract public void close() throws IOException;	//关闭通道
```

#### FileWriter 构造方法：

```java
public Filewriter(String FileName) throws IOExcepition;	//根据提供的文件路径构建一条文件输出通道

public Filewriter(String FileName, boolean append) throws IOException;	//根据提供的文件路径构建一条文件输出通道，并根据append的值来决定是将内容追加还是覆盖

public Filewriter(File file) throws IOException;	//根据提供的文件信息来构建一条文件输出通道

public Filewriter(File file, boolean append) throws IOException;	//根据提供的文件信息来构建一条文件输出通道，并根据append的值来决定是将内容追加还是覆盖
```



示例：

使用字符流将“夹心饼干真好吃”写入到磁盘文件中

```java
package com.Sonnet.IOStream;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class Example8 {
    public static void main(String[] args) {
        //必须要保证文件夹存在
        File file = new File("chapter17\\IO_Test\\writer.txt");
        File parentfile = file.getParentFile();
        //文件夹不存在就创建多级目录
        if (!parentfile.exists()) {
            parentfile.mkdirs();
        }
        //Writer类实现了AutoCloseable接口，因此可以将Writer类对象的构建方法放在try后面的()中
        try(Writer writer = new FileWriter("chapter17\\IO_Test\\writer.txt", true)) {
            String text = "夹心饼干超好吃";
            char[] values = text.toCharArray();
            for (char c : values) {
                writer.write(c);
            }
            writer.write(values, 0, 4);
            //强制将通道中的数据写入文件
            writer.flush();
        } catch(IOException e) {
            e.printStackTrace();
        }
    }
}

```

> Writer类实现了`AutoCloseable`接口，因此可以直接写在`try(){} catch{}`语句后面的（）中



#### Reader 常用方法：

```java
public int read() throws IOException;	//读取一个字符

public int read(char[] cbuf) throws IOException;	//读取字符到给定的字符数组中

abstract public int read(char cbuf[], int off, int len) throws IOException;	//将读取的字符按照偏移量和长度存储在字符数组中

abstract public void close() throws IOException;	//关闭通道
```

#### FileReader 构造方法:

```java
public FileReader(String fileName) throws FileNotFoundException;	//根据提供的文件路径构建一条文件输入通道

public FileReader(File file) throws FileNotFoundException;	//根据提供的文件信息构建一条文件输入通道
```

示例：

使用字符流将文件信息从磁盘中读取到内存中来，并在控制台输出

```java
package com.Sonnet.IOStream;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.io.Reader;

public class Example9 {
    public static void main(String[] args) {
        try(Reader reader = new FileReader("chapter17\\IO_Test\\writer.txt")) {
            StringBuilder sb = new StringBuilder();
            while (true) {
                int c = reader.read();
                if (c == -1) break;
                sb.append((char)c);
                //不强转打印的是数字
                System.out.print((char)c);
            }
            System.out.println();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        try (Reader reader = new FileReader("chapter17\\IO_Test\\io.txt")) {
            char[] buffer = new char[4096];
            int offset = 0;
            while (true) {
                int len = reader.read(buffer, offset, 30);
                if (len == -1) break;
                offset += len;
            }
            System.out.println(new String(buffer, 0, offset));
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

    }
}

```



练习：

使用字符流实现磁盘文件的拷贝

```java
package com.Sonnet.IOStream;

import java.io.*;

public class Example10 {
    public static void main(String[] args) {
        String resource = "chapter17\\IO_Test\\writer.txt";
        String dest = "chapter17\\IO_Test\\char_copy_test.txt";
        charCopy(resource, dest);
    }

    public static void charCopy(String resource, String dest) {
        File resourcefile = new File(resource);
        File destfile = new File(dest);
        File parentfile = destfile.getParentFile();
        //如果文件夹不存在就创建
        if (!parentfile.exists()) {
            parentfile.mkdirs();
        }

        try(Reader reader = new FileReader(resourcefile);
            Writer writer = new FileWriter(destfile)) {
            char[] buffer = new char[4096];
            while (true) {
                int len = reader.read(buffer);
                if (len == -1) break;
                writer.write(buffer, 0 , len);
            }
            writer.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```



### 3. 缓冲流

```txt
到目前为止，我们看到的大多数示例都使用无缓冲的I / O。这意味着每个读取或者写入请求均由操作系统直接处理。由于每个这样的请求通常会触发磁盘访问，网络活动或某些其他相对昂贵的操作，因此这可能会使程序的效率大大降低。

为了减少这种开销，Java平台实现了缓冲的I / O流。缓冲的输入流从称为缓冲区的存储区中读取数据；仅当缓冲区为空时，才调用本机输入API。同样，缓冲的输出流将数据写入缓冲区，并且仅在缓冲区已满时才调用本机输入API。

有四种用于包装非缓冲流的缓冲流类：BufferedInputStream和BufferedOutputStream创建缓冲的字节流，而BufferedReader和BufferedWriter创建缓冲的字符流。
```



#### BufferedOutputStream 构造方法：

```java
public BufferedOutputStream(OutputStream out);	//根据给定的字节输出流创建一个缓冲输出流，缓冲区大小使用默认大小

public BufferedOutputStream(OutputStream out, int size);	//根据给定的字节输出流创建一个缓冲输出流，并指定缓冲区的大小
```

#### BufferedInputStream 构造方法：

```java
public BufferedInputStream(InputStream in);	//根据给定的字节输入流创建一个缓冲输入流，缓冲区大小使用默认大小

public BUfferedInputStream(InputStream in, int size);	//根据给定的字节输入流创建一个缓冲输入流，并指定缓冲区的大小
```

示例：

使用缓冲字节流实现磁盘拷贝的功能

```java
package com.Sonnet.IOStream;

import java.io.*;

public class Example11 {
    public static void main(String[] args) {
        File file = new File("chapter17\\IO_Test\\buffer_test");
        File parent = file.getParentFile();
        if (!parent.exists()) {
            parent.mkdirs();
        }
        try(InputStream is = new FileInputStream("chapter17\\IO_Test\\io.txt");
            BufferedInputStream bis = new BufferedInputStream(is);
            OutputStream os = new FileOutputStream("chapter17\\IO_Test\\buffer_test");
            BufferedOutputStream bos = new BufferedOutputStream(os)) {
            byte[] buffer = new byte[4096];
            while (true) {
                int len = bis.read(buffer);
                if (len == -1) break;
                bos.write(buffer, 0, len);
            }
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```



#### BufferedWriter 构造方法：

```java
public BufferedWriter(Writer out);	//根据给定的字符输出流创建一个缓冲字符输出流，缓冲区大小使用默认大小

public BufferedWriter(Wrier out, int size);	//根据给定的字符输出流创建一个缓冲字符输出流，并指定缓冲区大小
```

#### BufferedReader 构造方法：

```java
public BufferedReader(Reader in);	//根据给定的字符输入流创建一个缓冲字符输入流，缓冲区大小使用默认大小

public BufferedReader(Reader in, int size);	//根据给定的字符输入流创建一个缓冲输入流，并指定缓冲区的大小
```



示例：

```java
package com.Sonnet.IOStream;

import java.io.*;

public class Example12 {
    public static void main(String[] args) {
        String resource = "chapter17\\IO_Test\\io.txt";
        String dest = "chapter17\\IO_Test\\char_buffer.txt";
        bufferedSteam(resource, dest);
    }

    public static void bufferedSteam(String resource, String dest) {
        File file = new File(dest);
        File parent = file.getParentFile();
        if (!parent.exists()) {
            parent.mkdirs();
        }

        try(Reader reader = new FileReader(resource);
            BufferedReader br = new BufferedReader(reader);
            Writer writer = new FileWriter(file);
            BufferedWriter bw = new BufferedWriter(writer)) {
            char[] buffer = new char[4096];
            while (true) {
                int len = br.read(buffer);
                if (len == -1) break;
                bw.write(buffer, 0 , len);
            }
            bw.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```



示例：

`Buffered`的特有方法：读取一整行，写入一整行 并 实现文件拷贝

```java
package com.Sonnet.IOStream;

import java.io.*;

public class Example13 {
    public static void main(String[] args) {
        String path = "chapter17\\buffer\\buffer1.txt";
        String source = "chapter17\\buffer\\buffer1.txt";
        String dest = "chapter17\\buffer\\copy.txt";

        writerFile(path);
        readerFile(path);
        //文件拷贝
        copyFile(source, dest);

    }

    private static void readerFile(String path) {
        File file = new File(path);
        File parent = file.getParentFile();
        //如果父级文件不存在,创建多级目录
        if (!parent.exists()) {
            parent.mkdirs();
        }
        //创建一条文件输入通道
        try (Reader reader = new FileReader(file);
             //创建缓冲字符输入流
             BufferedReader bw = new BufferedReader(reader)) {
            char buf[] = new char[4096];
            while (true) {
                //读取一整行的内容，不包括换行字符
                //如果到达末端未读取任何字符返回null
                String line = bw.readLine();
                //读取到末尾
                if (line == null) break;
                System.out.println(line);
            }
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private static void writerFile(String path) {
        File file = new File(path);
        //判断父级目录存不存在
        if (!file.getParentFile().exists()) {
            file.getParentFile().mkdirs();
        }
        //因为Writer类实现了AutoCloseable接口，可以写在try（）{}cahtch{}的（）中
        //创建一条文件输出流
        try (Writer writer = new FileWriter(file);
             //创建缓冲字符输出流
             BufferedWriter bw = new BufferedWriter(writer);
             Reader reader = new FileReader(file)) {
            bw.write("这是第一行");
            //换行
            bw.newLine();
            bw.write("这是第二行");
            bw.newLine();
            bw.write("这是第三行");
            bw.flush();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private static void copyFile(String source, String dest) {
        File sourceFile = new File(source);
        File destFile = new File(dest);
        //获取父级文件
        File parent = destFile.getParentFile();
        //如果父级文件不存在就创建多级目录
        if (!parent.exists()) parent.mkdirs();

        //创建通道
        try(Reader reader = new FileReader(sourceFile);
            BufferedReader br = new BufferedReader(reader);
            Writer writer = new FileWriter(destFile);
            BufferedWriter bw = new BufferedWriter(writer)) {
            //读取
            char[] buf = new char[4096];
            while (true) {
                //读取一整行
                String line = br.readLine();
                //读取到末尾
                if (line == null) break;
                //写入
                bw.write(line);
                //换行
                bw.newLine();
            }
            bw.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```



### 4. 数据流

数据流支持原始数据类型（布尔值、char、字节、short、int、long、folat和double）已经String的二进制I / O，所有数据流都实现`DataInput`接口和`DataOutput`接口。



#### `DataOutput` 接口常用方法：

```java
void writeBoolean(boolean V) throws IOException;	//将布尔值作为一个字节写入底层输出通道

void writeByte(int V) throws IOException;	//将字节写入底层输出通道

void writeShort(int V) throws IOException;	//将短整型作为2个字节（高位在前）写入底层通道

void writeChar(int V) throws IOException;	//将字符作为2个字节（高位在前）写入底层通道

void writeInt(int V) throws IOException;	//将整数作为4个字节（高位在前）写入底层通道

void writeLong(long V) throws IOException;	//将长整型作为8个字节（高位在前）写入底层通道

void writeFlout(float V) throws IOException;	//将单精度浮点数作为4个字节（高位在前）写入底层通道

void writeDouble(double V) throws IOException;	//将双精度浮点数作为8个字节写入底层通道

void writeUTF(String S) throws IOException;	//将UTF-8编码格式的字符串以与机器无关的方式写入底层输出通道
```



`DataOutputStream` 构造方法：

```java
public DataOutputStream(OutputStream out);	//根据给定的字节输出流创建一个二进制数据输出流
```

示例：

```java
    private static void dataWrite(String path) {
        File file  = new File(path);
        File parent = file.getParentFile();
        //父级文件不存在, 创建多级目录
        if (!parent.exists()) parent.mkdirs();
        //创建一体条文件输出流
        try (OutputStream os = new FileOutputStream(file);
            //创建二进制数据输出流
             DataOutputStream dos = new DataOutputStream(os) ){
            dos.writeBoolean(true);
            dos.writeByte(-1);
            dos.writeShort(-2);
            dos.writeChar('a');
            dos.writeInt(-3);
            dos.writeFloat(4.0f);
            dos.writeDouble(100.0);
            dos.writeLong(400L);
            dos.writeUTF("夹心饼干");
            dos.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

```



#### `DataInput` 接口常用方法：

```java
boolean readBoolean() throws IOEception;	//读取一个字节， 如果为0，非返回false， 否则返回true

byte readByte() throws IOException;	//读取一个字节

int readUnSignedByte() throws IOException;	//读取一个字节，返回0-255之间的整数

short readShort() throws IOException;	//读取2个字节， 返回一个短整数

int readUnsignedShort() throws IOException;	//读取2个字节，返回0-65535之间的整数

char readChar() throws IOException;	//读取2个字节， 返回1个字符

int readInt() throws IOException;	//读取4个字节，返回一个整数

long readLong() throws IOException;	//读取8个字节，返回一个长整数

float readFloat() throws IOException;	//读取4个字节，返回一个单精度浮点数

double readDouble() throws IOException;	//读取8个字节， 返回一个双精度浮点数

String readUTF() throws IOExcption;	//读取一个使用UTF-8编码格式的字符串
```



#### `DataInputStream` 构造方法：

```java
public DataInputStream(InputStream in);	//根据给定的字节输入流创建一个二进制输入流
```

示例：

```java
    private static void dataRead(String path) {
        File file = new File(path);
        //创建一条文件输入通道
        try(InputStream is = new FileInputStream(file);
            //创建二进制数据输入流
            DataInputStream dis = new DataInputStream(is)) {
            //读取， 要按写入的顺序读取, 不然会报EOFException异常
            boolean b = dis.readBoolean();
            System.out.println(b);
            byte by = dis.readByte();
            System.out.println(by);
            Short s = dis.readShort();
            System.out.println(s);
            char c = dis.readChar();
            System.out.println(c);
            int i = dis.readInt();
            System.out.println(i);
            float f = dis.readFloat();
            System.out.println(f);
            double d = dis.readDouble();
            System.out.println(d);
            long l = dis.readLong();
            System.out.println(l);
            String str = dis.readUTF();
            System.out.println(str);
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
            //要先捕捉EOFException（EndOfFile）
            //EOFException继承了IOException，先子类后父类
        } catch (EOFException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

```

> 注意： `DataInputStream`通过`EOFException`来检测文件结束条件，而不是测试无效的返回值。`DataInputStream`方法的所有实现都使用`EOFExcption`



### 5. 对象流

正如二进制数据支持原始数据类型的I / O一样，对象数据流也支持对象的I / O。大多数（但不是全部）标准类支持对象的序列化。那些类实现了序列化标记接口`Serializable`才能够序列化。



#### `ObjectOutput` 接口常用方法：

```java
public void writeObject(Object obj) throws IOException;	//将对象写入底层通道
```



#### `ObjectOutputStream` 构造方法：

```java
public ObjectOutputStream(Output out) throws IOException;	//根据给定的字节输出流创建一个对象输出流
```

示例：

```java
package com.Sonnet.Object;

import java.io.Serializable;

//实现Serializable接口，实现序列化
public class Student implements Serializable {
    public String name;
    public int age;

    public Student() {};

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

```java
package com.Sonnet.Object;

import java.io.*;

public class test {
    public static void main(String[] args) {
        String path = "chapter17\\object\\test.txt";
        File file = new File(path);
        File parent = file.getParentFile();
        //父类文件不存在，就创建父级目录
        if (!parent.exists()) parent.mkdirs();
        try(//创建一条文件输出流
            OutputStream os = new FileOutputStream(file);
            //创建一条对象输出流
            ObjectOutputStream oos = new ObjectOutputStream(os);) {
            oos.writeObject(new Student("zhangsan", 18));
            oos.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```

将一个对象从内存中写入磁盘文件的过程称之为序列化。**序列化必须要求该对象所有类型实现序列化接口`Serializable`。**



#### `ObjectInput` 接口常用方法：

```java
public Object readObject() throws IOException;	//读取一个对象
```



#### `ObjectInputStream`  构造方法：

```java
public ObjectInputStream(InputStream in) throws IOException;	//根据给定的字节输入流创建一个对象输入流
```

示例：

```java
package com.Sonnet.Object;

import java.io.Serializable;

//实现Serializable接口，实现序列化
public class Student implements Serializable {
    public String name;
    public int age;

    public Student() {};

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

```java
public class test {
    public static void main(String[] args) {
        String path = "chapter17\\object\\test.txt";
        try(//创建一条文件输入通道
            InputStream is = new FileInputStream(path);
            //创建一条对象输入通道
            ObjectInputStream ois = new ObjectInputStream(is);) {
            //注意强转
            Student s = (Student) ois.readObject();
            System.out.println(s);
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
    }

```

将一个对象从磁盘文件中读取到内存中的过程称之为反序列化。需要注意的是，反序列化必须保证与序列化使用的`JDK`版本一致。



### 6. 字节流转字符流

示例：

```java
package com.Sonnet.reverse;

import java.io.*;

public class test {
    public static void main(String[] args) {
        String path = "chapter17\\reverse\\test.txt";
        write(path);
        read(path);
    }

    private static void write(String path) {
        File file = new File(path);
        File parent = file.getParentFile();
        if (!parent.exists()) parent.mkdirs();
        try(OutputStream os = new FileOutputStream(file);
            OutputStreamWriter ops = new OutputStreamWriter(os);
            BufferedWriter bw = new BufferedWriter(ops);) {
            String[] lines = {
                    "jiaxin",
                    "binggan",
                    "zhenhaochi"
            };
            for (String s : lines) {
                bw.write(s);
                bw.newLine();
            }
            bw.flush();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private static void read(String path) {
        try(InputStream is = new FileInputStream(path);
            InputStreamReader isr = new InputStreamReader(is);
            BufferedReader br = new BufferedReader(isr);) {
            while (true) {
                String line = br.readLine();
                if (line == null) break;
                System.out.println(line);
            }
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```

