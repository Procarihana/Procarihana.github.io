---
title: "JAVA IO"
date: 2020-08-30T15:29:44+08:00
draft: false
toc: false
images: 
tags: [JavaIO,]
---

- cpu 负责所有运算
- 3GHz 每秒运行30亿条指令，做一件事情只需要0.38纳秒

- - SSD（固态硬盘）：寻址时间太慢了，和CPU形成矛盾
- - 内存：存数据，断电丢失
- - 硬盘（硬盘驱动器） 存数据容量大，断电不丢失

- 一个文件本质上是一段字节流，由一个执行程序决定怎么执行一段字节流（16进制）

字节范围 0～255
## 一切文件的本质
- 一段字节流：
- - 文本文件（txt/代码/HTML等）
- - 二进制文件
- 每个程序负责解释文件中的字节流

输入输出是站在程序的角度

## InputStream/PutputStream
- 抽象的输入/输出操作
- - 从文件读取字节流 【实战】

 value ==-1 的时候，循环结束
- -从网络读取字节流【实战】
- -从其他  
**最好使用绝对路径**  
抽象类不能直接实例化，因为抽象类里面包含还没有实现的方法
- InputStream…… 这种只是抽象方法，不能够直接实现
- 用ProcessBuilder建立新的进程


## Java中的File
- file 代表的是文件路径
- 抽象的“文件”：文件或者文件夹
- File 的常见方法  
- - file.exists 判断文件是否存在
    

         .listfile 当前文件下有什么文件
         .isAbsolute 是否是绝对路径
         .getname
         .isDirectory 是否是文件夹
         .getAbsolutePath
         .listFile
         

- - file.isfile 判断是不是文件
- - file.isDirectory 判断路径是不是分拣家
- - getAbsolutePath 获得绝对路径
- - listfiles 查看当前文件夹下面有什么文件
- - new File（目录名字，子目录的名字）快速构建一个新的子目录
- - file.walkFileTree 遍历
- - readAllLine（不用依赖第三方库）
- 绝对路径与相对路径
- - 相对路径只有在相对一个路径时候才有效
- - 相对的就是JVM进程的当前工作目录
- - 绝对路径只会对应一个文件
- 读/写文件
- - 解决IO速度慢
- - - BufferReader/Writer
- - - 换行符
- - - BufferReader - 一次性读好多东西到缓冲区里面
- - - BufferWriter - 一次性写好多东西
- NIO的Path（Java 7+）
LInxu 硬连接和软连接
- New IO 新的IO
- Non -blocking IO 非阻塞IO
- NIO的Path - 就是旧版本的File
- - 经典的IO模型基于流
- - 优点：容易理解，方便抽象
- - 缺点：慢（流：一个字节一个字节按顺序）
## 解决IO太慢
- BufferedReader/Writer 
- - BufferedReader - ⼀次性读取好多东⻄到缓冲区⾥
- - BufferedWriter - ⼀次性写好多东⻄到缓冲区⾥
- 在内存中创建好，⼀次写⼊  

path.tofile --
file.topath 
两个东西相互转换

Buffer是一个缓冲区，是内存的一块

块和块之间是没有顺序的，所以可以同时写

写入：
1. 一个一个字节地写
2. 存一些后一次性写入（并发；多线程）//CPU在等待IO的时候做其他事情



## HomeWork
#### 多种方法写入读出 
- Apache Commons IO 
- - 通过·FilesUtils.readFileToString` 读取 `writeStringToFile`写入
```java
 public static List<String> readFile2(File file) throws IOException {
        return Arrays.asList(FileUtils.readFileToString(file, "utf8").split("\n"));
    }

public static void writeLinesToFile2(List<String> lines, File file) throws IOException {
        String result = new String();
        for (String s : lines) {
            result += s;
            result += "\n";
        }
        FileUtils.writeStringToFile(file, result, "utf8");
    }
```
- FileInputStream 和FileOutPutStream
- - 注意getByte 得到的是字节流
- - 读取迭代的过程中，要记得readInt 需要不断地从fileInputStream里面读入，否则就会一直只读第一个字节，变成死循环
- - InputStream 结束的标志就是`-1`
```java
    public static List<String> readFile1(File file) throws IOException {
        FileInputStream fileInputStream = new FileInputStream(file);
        int readInt = fileInputStream.read();
        String s = new String();
        while (readInt != -1) {
            s += (char) readInt;
            readInt = fileInputStream.read();
        }
        fileInputStream.close();
        return Arrays.asList(s.split("\n"));
    }

  public static void writeLinesToFile1(List<String> lines, File file) throws IOException {
        FileOutputStream fileOutputStream = new FileOutputStream(file);
        for (String s : lines) {
            byte[] bytes = s.getBytes();
            fileOutputStream.write(bytes);
            fileOutputStream.write('\n');
        }
        fileOutputStream.close();
    }
```

- BufferReader和BufferWriter
- - BufferReader 继承 Reader ，而Reader 实现closeable 接口，所以BufferReader 需要关闭
- - BufferReader 是有锁的，所以是线程安全的方法
- - BufferReader.readLine()只是read一行，所以要记得用循环
- - BufferReader.readLine()到达末尾的时候会返回null，如果直接用是否为null判断是否结束的话，会造成死循环（readline不断地返回null），要用一个变量来接受方法的返回值来间接判断。
```java
 public static List<String> readFile1(File file) {
        List<String> result = new LinkedList<>();
        String readLines= new String();
        String checkReadLine = new String();
        try (BufferedReader bufferedReader = new BufferedReader(new FileReader(file))) {
            while ((checkReadLine = bufferedReader.readLine()) != null){
               readLines += checkReadLine;
               readLines += "\n";
            }
            result = Arrays.asList(readLines.split("\n"));
        } catch (IOException e) {
            e.printStackTrace();
        }
        return result;
    }
        public static void writeLinesToFile1(List<String> lines, File file) throws IOException {
        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(file));
        for (String s : lines) {
            bufferedWriter.write(s);
        }
    }       
```
- 使用Java 7+的NIO引入的`Files.readAllBytes()`/`Files.readAllLines()`/`Files.write()`方法
```java
  public static List<String> readFile3(File file) throws IOException {
        return Files.readAllLines(file.toPath());
    } 
       public static void writeLinesToFile3(List<String> lines, File file) throws IOException {
        Files.write(file.toPath(), lines);
    }
```
 - 实现
 ```java
    public static void main(String[] args) throws IOException {
        File projectDir = new File(System.getProperty("basedir", System.getProperty("user.dir")));
        File testFile = new File(projectDir, "target/test.txt");
        List<String> lines = Arrays.asList("AAA", "BBB", "CCC");
        writeLinesToFile1(lines, testFile);
        writeLinesToFile2(lines, testFile);
        writeLinesToFile3(lines, testFile);

        System.out.println(readFile1(testFile));
        System.out.println(readFile2(testFile));
        System.out.println(readFile3(testFile));
        }
    }
```
#### 读取网络内容———>csvFile
- csvFile：逗号分隔值（Comma-Separated Values，CSV，有时也称为字符分隔值，因为分隔字符也可以不是逗号），其文件以纯文本形式存储表格数据（数字和文本）。 纯文本意味着该文件是一个字符序列，不含必须像二进制数字那样被解读的数据。
- 爬取github 信息可以用 GitHub 库的 connectAnonumously匿名链接去爬取
- 用GHRespository 来获得github仓库的名字
- 通过GHPullRequest 来发出pull请求，获得仓库的内容,用GHPullRequest里面get的方法来过滤内容
- - getUser（）获得的是GitHub User 的内容，需要getLogin（）才能够过滤出用户名字
- - 在过滤的过程中要手动添加","来分隔内容，达到csvFile 的要求


```java
    // 给定一个仓库名，例如"golang/go"，或者"gradle/gradle"，读取前n个Pull request并保存至csvFile指定的文件中，格式如下：
    // number,author,title
    // 12345,blindpirate,这是一个标题
    // 12345,FrankFang,这是第二个标题
    public static void savePullRequestsToCSV(String repo, int n, File csvFile) throws IOException {
        GitHub github = GitHub.connectAnonymously();
        GHRepository repository = github.getRepository(repo);
        List<GHPullRequest> pullRequests = repository.getPullRequests(GHIssueState.OPEN);

        String title = "number,author,title";
        List<String> lines = new ArrayList<>();
        List<String> body = pullRequests.stream().map(Crawler::getLine).collect(Collectors.toList());
        lines.add(title);
        for (int i = 0; i < n; i++) {
            lines.addAll(Collections.singleton(body.get(i)));
        }


        Files.write(csvFile.toPath(), lines);
    }

    static String getLine(GHPullRequest pullRequest) {
        try {
            return pullRequest.getNumber() + "," + pullRequest.getUser().getLogin() + "," + pullRequest.getTitle();
        } catch (IOException e) {
            throw new UncheckedIOException(e);
        }
    }
    }
```


