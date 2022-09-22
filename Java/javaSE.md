# 访问权限修饰符

**类成员在不同范围是否可以被访问：**

![image-20211030175402363](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211030175402363.png)

> private 私有的 ：只有本类可以访问其余类都不可以
>
> protected 保护的： 本类及其子类可以访问（父子友好），同一个包中的其他类也可以访问（包内友好）
>
> public 公有的：不受任何限制，本类和非本类均可随意访问
>
> 缺省(无修饰)：只有相同包中的类可以访问
>
> 访问权限修饰符既可以修饰变量也可以修饰方法

# IO流

## 流的概述：

一组有顺序的，有起点和重点的字节集合。是对设备文件间数据传输的总称和抽象；

是指一连串流动的字符以先进先出的方式发送信息的通道；

## 流的分类：

### 按流向分

输出流：OutputStream和Writer为基类（向内存外发送数据）

输入流：InputStram和Reader为基类（流入内存）

### 按处理数据单元分

字节流：操作二进制文件

字节输入流：InputStream基类

字节输出流：OutputStream基类

字符流：

字符输入流：Reade基类 

字符输出流：Writer基类 

## 节点流和处理流

![image-20211027194658523](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027194658523.png)

### 节点流

可以从一个特定的数据源处理数据，如FileReader，FileWriter

节点流是底层流，与数据源相连

### 处理流

处理流（也叫包装流）是“连接”在已经存在的流（节点流或处理流）之上，为程序提供更强大的读写功能，也更加灵活，如BuffereReader，BufferReader

处理流对节点流进行包装，使用了修饰器的模式，不会直接与数据源相连

## File类：

一个File类的对象表示了磁盘上的目录或文件。

File类提供了与平台无关的方法来对磁盘上的文件或目录进行操作；

File类直接处理或文件系统；没有指定信息怎样从文件读取或向文件存储；

描述了文件本身的属性；

File对象用来获取或处理与磁盘文件相关的信息；

file类还可以浏览子目录层次结构

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211026124826165.png" alt="image-20211026124826165" style="zoom: 67%;" />

### 创建文件对象相关的构造方法：

![image-20211029232654012](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211029232654012.png)

#### 示例：

```java
//方式一：new File(String pathname)
    @Test
    public void  create(){
        String pathname = "e:/Test/new01.txt";
        File file = new File(pathname);
        try {
		//创建文件
            file.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("文件创建成功！！！");
    }

//    方式二：new File(File parent,String chile)
    @Test
    public void create02(){
        File parent = new File("e:/Test");
        String chile = "news02.txt";
        File file = new File(parent,chile);
        try {
		//创建文件
            file.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("文件创建成功！！！");
    }

//    方式三：new File(String parent,String chile)
    @Test
    public void create03(){
        File file = new File("e:/Test","news03.txt");
        try {
        //创建文件
            file.createNewFile();
        }catch (Exception e){
            e.printStackTrace();
        }
        System.out.println("文件创建成功！！！");
    }
```

### 常用操作

#### 获取文件相关信息

getName、getAbsolutePath、getParent、length、exists、isFile、isDirctory

```java
//    获取文件信息
    @Test
    public void info(){
        //创建文件对象
        File file = new File("e:/Test/new01.txt");

        //调用响应的方法，得到对应的文件信息
        System.out.println("文件名："+file.getName());
        System.out.println("绝对路径："+file.getAbsolutePath());
        System.out.println("父级目录："+file.getParent());
        System.out.println("文件大小："+file.length()+" byte");
        System.out.println("文件是否存在："+file.exists());
        System.out.println("文件是否是文件："+file.isFile());
        System.out.println("文件是否是目录："+file.isDirectory());
    }
```

#### 目录的操作和文件删除

```java
    @Test
    public void m1(){
        //判断是否存在，如果存在就删除，不存在就创建目录
        File file = new File("e:/Test/a");
        if (file.exists()){
            System.out.println("该文件存在");
            //删除文件，会返回一个Boolean值，成功返回true，否则为false
            boolean i = file.delete();
            if (i){
                System.out.println("删除成功!!!");
            }else {
                System.out.println("删除失败");
            }
        }else {
            System.out.println("该目录不存在");
            //创建目录
            // 一级目录：file.mkdir();
            // 多级目录：file.mkdirs();
            boolean i = file.mkdir();
            if (i){
                System.out.println("创建成功");
            }else{
                System.out.println("创建失败");
            }
        }
    }
```

### 示例：

#### 显示文件夹及其子文件夹下的所有类容

```java
//显示目录下的所有文件
public class ShowFile {

    public static void main(String[] args) {
        //目录
        String filePath = "e:/Test";
        isFile(filePath);
    }
    private static void isFile(String filename){
        //创建文件对象
        File file = new File(filename);
        //获取目录下的所有文件名集合
        String[] list = file.list();
        //循环判断
        for (String name:list) {
            //当前文件的路径名
            String filename2 = filename + "/" + name;
            //创建当前文件对象
            File file1 = new File(filename2);
            //判断该文件是否是目录
            if (file1.isDirectory()){
                //当前文件是目录循环出里面的文件
                isFile(filename2);
            }else{
                //当前文件不是目录就直接输出文件名
                System.out.println(name);
            }
        }
    }
}
```

#### 复制文件夹

```java
public class CopyFiles {

    public static void main(String[] args) {
        String filename1 = "e:/Test";
        String filename2 = "e:/Test2";
        isFile(filename1,filename2);
    }

    //复制文件夹
    public static void isFile(String filename1,String filename2){
        //原文件夹
        File file1 = new File(filename1);
        //新文件夹
        File file2 = new File(filename2);
        //获取原文件夹的所有文件
        String[] files = file1.list();
        //遍历文件
        for (String fileName:files) {
            //原文件与新文件对应的路径
            File fileChile = new File(filename1+"/"+fileName);
            File copyChile = new File(filename2+"/"+fileName);
            //判断文件是否为目录
            if (fileChile.isDirectory()){
                //是目录就在对应的新文件夹中创建对应的目录
                copyChile.mkdirs();
                //然后将这个目录复制到新文件夹对应的路径
                isFile(filename1+"/"+fileName,filename2+"/"+fileName);
            }else {
                //当文件不是目录，就使用输入输出流将其复制到新文件夹对应的路径
                BufferedInputStream bufferedInputStream = null;
                BufferedOutputStream bufferedOutputStream = null;
                try {
                    //创建输入输出流
                    bufferedInputStream = new BufferedInputStream(new FileInputStream(fileChile));
                    bufferedOutputStream = new BufferedOutputStream(new FileOutputStream(copyChile));
                    int buf = 0;
                    byte[] bytes = new byte[10];
                    //循环读取文件中的数据，并写入到新文件
                    while ((buf = bufferedInputStream.read(bytes)) != -1){
                        bufferedOutputStream.write(bytes,0,buf);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    //关闭输入输出流
                    try {
                        bufferedInputStream.close();
                        bufferedOutputStream.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```

## FileInputStream：文件输入流（字节）

### 方法摘要：

![image-20211026230304117](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211026230304117.png)

### 示例：

```java
//    FileInputStream的使用： 文件------>程序
    @Test
    public void readerFile01() throws IOException {
        String filePath = "e:/Test/hello.txt";
        int readNum = 0;
        FileInputStream fileInputStream = null;
        byte[] buf = new byte[8];
        try {
            //创建FileInputStream对象，用于读取文件
            fileInputStream = new FileInputStream(filePath);
            /**
             * 从此输入流中读取一个数据字节。没有输入可以，返回值为-1
             * 返回值为-1，表示读取完毕
             * while ((readNum = fileInputStream.read()) != -1){
             *      System.out.println((char) readNum);//将得到的数据字节强转成char类型显示
             * }
             */
            //如果读取正常，返回实际读取的字节数
            while ((readNum = fileInputStream.read(buf)) != -1){
                System.out.println(new String(buf,0,readNum));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            fileInputStream.close();
        }
    }
```

## FileOutputStream：文件输出流（字节）

### 方法摘要：

![image-20211026230111832](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211026230111832.png)

### 示例：

```java
    //    FileOutputStream的使用：程序------>文件
    @Test
    public void writeFile() {
        FileOutputStream fileOutputStream = null;
        String filePath = "e:/Test/fileOutputStream.txt";
        try {
            //在创建输出流时，添加true，表示添加的内容会放在文件的末尾，不会直接覆盖原文件
            fileOutputStream = new FileOutputStream(filePath,true);
            /**
            * 写入一个字节
            *fileOutputStream.write('H');
            */
            //写入字符串
            String str = "  wdnmd";
            //使用getBytes将字符串转为字节数组
            fileOutputStream.write(str.getBytes());
            System.out.println("写入成功");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## 文件拷贝

将 e:/Test 下的 a.png 文件拷贝到 e:/Test/a 下

```java
//文件拷贝
public class FileCopy {
    /**
     * 思路分析：
     * 创建文件输入流，读取原文件
     * 创建文件输出流，将读取到的数据写入新文件
     */
    @Test
    public void Copy(){
        String f1 = "e:/Test/a.png";
        String f2 = "e:/Test/a/a.png";
        FileInputStream fileInputStream = null;
        FileOutputStream fileOutputStream = null;
        byte[] buf = new byte[8];
        Integer i = 0;

        try {
            fileInputStream = new FileInputStream(f1);
            fileOutputStream = new FileOutputStream(f2,true);
            while ((i = fileInputStream.read(buf)) != -1){
                fileOutputStream.write(buf,0,i);
            }
            System.out.println("复制成功");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                if (fileInputStream != null){
                    fileInputStream.close();
                }
                if (fileOutputStream != null){
                    fileOutputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## FileReader：文件输入流（字符）

### 方法摘要：

![image-20211027115741832](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027115741832.png)

### 示例：

```java
    @Test
    public void FileReaderTest(){
        String filePath = "e:/Test/hello.txt";
        int i = 0;
        char[] buf = new char[3];
        FileReader fileReader = null;
        //创建FileReader对象
        try {
            fileReader = new FileReader(filePath);
            //读取多个字符
            while ((i = fileReader.read(buf)) != -1){
                System.out.print(new String(buf,0,i));
            }
            //读取单个字符
//            while ((i = fileReader.read()) != -1){
//                System.out.println((char)i);
//            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## FileWriter：文件输出流（字符）

<font color="red">**使用后必须关闭或刷新后才能成功写入**</font>

### 方法摘要：

![image-20211027120405411](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027120405411.png)

### 示例：

```java
    @Test
    public void FileWriter01(){
        String filePath = "e:/Test/hello.txt";
        FileWriter fileWriter = null;
        try {
            //添加true比表示追加，否则会覆盖全部文件
            fileWriter = new FileWriter(filePath,true);
            String str = "Hello World！";
            //写入数据
            fileWriter.write(str);
            System.out.println("写入成功");
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                //刷新
                fileWriter.flush();
                //关闭
//         	    fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## BufferedReader：缓冲输入流（字符）

### 方法摘要：

![image-20211027203201509](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027203201509.png)

### 示例：

```java
	@Test
    public void BufferedReader01(){
        String str = "e:/Test/hello.txt";
        BufferedReader bufferedReader = null;
        try {
            //创建节点流FileReader
            FileReader fileReader = new FileReader(str);
            //创建包装流BufferedReader，包装节点流fileReader
            bufferedReader = new BufferedReader(fileReader);
            //读取数据
            String line;//按行读取
            while ((line = bufferedReader.readLine()) != null){
                System.out.println(line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                //关闭流，只需要关闭 bufferedReader，底层会自动关闭 fileReader
                bufferedReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## BufferedWriter：缓冲输出流（字符）

### 方法摘要：

![image-20211027203453246](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027203453246.png) 

### 示例：

```java
    @Test
    public void BufferedWriter01(){
        String str = "e:/Test/hello.txt";
        BufferedWriter bufferedWriter = null;
        try {
            //创建包装流
            bufferedWriter = new BufferedWriter(new FileWriter(str,true));
            //写入数据
            bufferedWriter.newLine();//插入一个与相同相关的换行符
            bufferedWriter.write("包装流写入的数据");
            System.out.println("写入成功！");
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                //关闭处理流的底层会关闭 FileWriter
                bufferedWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## 使用包装流拷贝文本文件

```java
    @Test
    public void Copy02(){
        BufferedWriter bufferedWriter = null;
        BufferedReader bufferedReader = null;
        String data;
        try {
            bufferedReader = new BufferedReader(new FileReader("e:/Test/hello.txt"));
            bufferedWriter = new BufferedWriter(new FileWriter("e:/Test/a/hello.txt"));
            while ((data = bufferedReader.readLine()) != null){
                bufferedWriter.write(data);
                bufferedWriter.newLine();
            }
            System.out.println("复制成功");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                bufferedReader.close();
                bufferedWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## BufferedInputStream：缓冲输入流（字节）

**使用方法与BufferedReader相似**

### 方法摘要：

![image-20211027210151173](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027210151173.png)

## BufferedOutputStream：缓冲输入流（字节）

**使用方法与BufferedWriter相似**

### 方法摘要：

![image-20211027210221755](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211027210221755.png)

## 使用Buffered包装流拷贝二进制文件

```java
    @Test
    public void Copy03(){
        BufferedInputStream bufferedInputStream = null;
        BufferedOutputStream bufferedOutputStream = null;
        byte[] data = new byte[100];
        int i = 0;
        try {
            //创建缓冲输入流和缓冲输出流
            bufferedInputStream = new BufferedInputStream(new FileInputStream("e:/Test/a.png"));
            bufferedOutputStream = new BufferedOutputStream(new FileOutputStream("e:/Test/a/b.png"));
            //循环读取文件内容，并写入到新文件
            while ((i = bufferedInputStream.read(data)) != -1){
                bufferedOutputStream.write(data,0,i);
            }
            System.out.println("文件拷贝成功");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                //关闭包装流
                bufferedInputStream.close();
                bufferedOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## 对象处理流

### 序列化和反序列化

序列化就是在保存数据时，保存数据的值的数据类型

反序列化就是在恢复数据时，恢复它的数据类型和数据的值

需要让某个对象支持序列化机制，则必须让其类是可序列化的，即该类必须实现以下两个接口之一：

```java
Serializable           //标记接口，没有方法（推荐使用）
Externalizable      //该接口有方法需要实现
```

### 注意事项和细节说明

> 读写顺序要一致
>
> 要求序列化和反序列化的对象需要实现 Serializable 或 Externalizable 接口
>
> 序列化类中建议添加 SerialVersionUID，为了提高版本的兼容性
>
> 序列化对象，默认将里面所有的属性都进行了序列化，除了 static 和 transient 修饰的成员
>
> 序列化对象时，要求里面属性的类型也需要实现序列化接口；例如Dog类中用到了animal 类，则animal 也需要实现序列化接口
>
> 序列化具备可继承性，也就是如果某类已经实现了序列化，则它所有的子类默认实现了序列化

### ObjectOutputStream：对象输出流

#### 示例：

```java
public class Object_ {

    @Test
    public void ObjectOutputStream_(){
        String filePath = "e:/Test/data.dat";
        ObjectOutputStream objectOutputStream = null;
        try {
            //创建对象输入流
            objectOutputStream = new ObjectOutputStream(new FileOutputStream(filePath));
            //写入数据
            objectOutputStream.writeInt(100);
            objectOutputStream.writeBoolean(true);
            objectOutputStream.writeDouble(9.5);
            objectOutputStream.writeUTF("写入成功了");
            Dog dog = new Dog("yyb",2);
            objectOutputStream.writeObject(dog);
            System.out.println("写入成功");
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                objectOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

//实现Serializable接口才能序列化
class Dog implements Serializable{
    private String name ;
    private int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

### ObjectInputStream：对象输入流

```java
@Test
    public void ObjectInputStream_(){
        String filePath = "e:/Test/data.dat";
        ObjectInputStream objectInputStream = null;
        try {
            //创建对象输入流
            objectInputStream = new ObjectInputStream(new FileInputStream(filePath));
            /**读取文件信息
             * 注意：
             *      读取（反序列化）的顺序必须和写入（序列化）的顺序相同
             */
            System.out.println(objectInputStream.readInt());
            System.out.println(objectInputStream.readBoolean());
            System.out.println(objectInputStream.readDouble());
            System.out.println(objectInputStream.readUTF());
            Object dog = objectInputStream.readObject();
            /**
            * 获取到dog对象后，如果想要使用期中的某个方法，需要向下转型（将读取的Object类型的对象，转换成Dog类型）
            * 向下转型使用的Dog类需要和保存时的类是同一个
            */
            System.out.println(dog);

        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }finally {
            try {
                objectInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## 标准输入输出流

System.in：标准输入（键盘输入）

System.out：标准输出（控制台输出）

### 示例：

```java
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入：");
        String str = sc.next();
        System.out.println("您输入的是:"+str);
    }
```

## InputStreamReader 和 OutputStreamWriter：转换流

InputStreamReader：将字节输入流转换成字符输入流

OutputStreamWriter：将字节输出流转换成字符输出流

### 示例：

```java
	public static void main(String[] args) {
        //读取 e:/Test/hello.txt
        int i = 0;
        String str = null;
        BufferedReader bufferedReader = null;
        InputStreamReader inputStreamReader = null;
        try {
            //使用转换流，将 FileInputStream（字节流） 转换成 inputStreamReader（字符流）
            inputStreamReader = new InputStreamReader(new FileInputStream("e:/Test/hello.txt"),"gbk");
            //使用 BufferedReader 包装 inputStreamReader
            bufferedReader = new BufferedReader(inputStreamReader);
            while ((str = bufferedReader.readLine()) != null){
                System.out.println(str);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                bufferedReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

## 打印流

打印流只有输出流，没有输入流

```java
PrintWriter   //字符
PrintStream   //字节
```

### PrintStream：字节

```java
    public static void main(String[] args) {
        PrintStream out = System.out;
        out.println("hello");
        PrintStream out2 = null;
        try {
            out2 = new PrintStream(new FileOutputStream("e:/Test/hello.txt",true));
            out2.println("PrintStream");
            System.out.println("打印成功");
            
            //修改默认输出位置
            System.setOut(new PrintStream("e:/Test/t1.txt"));
            System.out.println("hello");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }finally {
            out.close();
            out2.close();
        }
    }
```

### PrintWriter：字节

使用方式与 PrintStream 相同

## Propertise

用于读取键值对形式的配置文件

### 示例：

```java
 public static void main(String[] args) {
        //读取propertise文件
        Properties properties = new Properties();
        Properties properties2 = new Properties();
        try {
//            查找数据
            properties.load(new FileReader("E:\\IDEA_work\\IO\\IoTest\\src\\main\\resources\\mysql.properties"));
            System.out.println(properties.get("user"));
            System.out.println(properties.get("pass"));

            //添加键值对
            properties2.setProperty("user","wdnmd");
            properties2.store(new FileOutputStream("E:\\IDEA_work\\IO\\IoTest\\src\\main\\resources\\mysql.properties",true),null);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

# NIO

NIO，是在JDK4中新增的IO（即 New Input Output）

NIO是非阻塞式的，也称为 Non-Blocked IO，对应的传统的IO是阻塞式的，也称为BIO，即 Blocked IO。

NIO 是主要用于网络的IO，在网络中需要通过网卡传输数据，如果使用传统的 BIO ，在网卡忙碌或者没有准备好的情况下，会降低程序的执行效率，使用非阻塞式的 NIO ，在网卡没有就绪的情况下，程序会执行其他操作，等网卡就绪后再传输数据

NIO有三个核心类：Buffer缓冲区，Channel通道，Selector选择器

## NIO 读取和写入文件示例

```java
    @Test
    public void Input_(){
        FileInputStream fileInputStream =null;
        FileOutputStream fileOutputStream = null;
        try {
            //创建文件输入输出的字节流
            fileInputStream = new FileInputStream("e:/Test/hello.txt");
            fileOutputStream = new FileOutputStream("e:/Test/hello2.txt");
            //获取与文件输出流（fileOutputStream）和输入流（fileInputStream）有关的唯一 FileChannel 对象
            FileChannel fosChannel = fileOutputStream.getChannel();
            FileChannel fileChannel = fileInputStream.getChannel();
            //将此通道的文件区域直接映射到内存中
            MappedByteBuffer map = fileChannel.map(FileChannel.MapMode.READ_ONLY, 0, fileInputStream.available());
            //返回指定 charset 的 charset 对象(设置字符编码)
            Charset charset = Charset.forName("gbk");
            //将此 map 中的字节解码成 Unicode 字符
            CharBuffer decode = charset.decode(map);
            //输出数据
            System.out.println(decode);
            //反转此缓冲区
            map.flip();
            //将字节序列从给定的缓冲区写入此通道
            fosChannel.write(map);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            //关闭输入流和输出流
            try {
                fileInputStream.close();
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

# 多线程

## 概述

进程（Process）：资源分配和调度的基本单位。（一个独立运行的程序，可包含多个线程）

线程（Thread）：CPU调度和执行的最小单位，包含多个线程。（进程中的一个执行路径）

> 并行：多个任务同时运行
>
> 并发：指多个任务同时请求运行。（处理器一次只能接受一个任务，就会把多个任务安排轮流执行，由于CPU时间片运行时间较短，就会感觉多个任务提示执行）

线程只能启动一次，一旦线程死亡，就不能再次执行（start()）

## 内存解读

线程A和线程B的堆内存和方法区内存共享，栈内存是独立的，一个线程执行就会创建一个运行时栈

![image-20211030225714612](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211030225714612.png)

## 线程的创建

### 继承Thread类

继承 Thread 的子类应重写 `Thread` 类的 `run` 方法

#### 示例：

```java
public class Thread_ {
    //main方法：主线程
    public static void main(String[] args) {
        //创建线程对象
        Thread thread = new TestThread();
        /**
        * 调用 start() 方法开启线程
        * start 方法的作用是在JVM中开辟一个新的栈空间，
        * start 方法的任务只是开辟栈空间，任务完成后，satrt方法就结束了，线程就启动成功了
        * 启动成功的线程会会自动调用 run 方法，run 方法在分支栈的最底部，main 方法在主栈的底部，他们是平级的
        */
        thread.start();

        for (int i = 0;i <= 300;i ++){
            System.out.print("main方法--》");
        }
    }
}

//继承Thread类，创建线程
class TestThread extends Thread{
    //重写run方法，里面就是业务代码
    @Override
    public void run() {
        for (int i = 0;i <= 10;i++){
            System.out.print("TestThread线程");
        }
    }
}
```

#### 运行效果：

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211030212905524.png" alt="image-20211030212905524" style="zoom: 80%;" />

#### 多线程同时下载多张图片的小案例

##### 代码

```java
public class ThreadDownloader extends Thread{
    private String url;
    private String fileName;

    public ThreadDownloader(String url,String filename){
        this.url = url;
        this.fileName = filename;
    }

    //run方法
    @Override
    public void run() {
        //调用WebDownloader的Downloader方法下载图片
        WebDownloader webDownloader = new WebDownloader();
        webDownloader.Downloader(url,fileName);
        System.out.println(fileName+"文件下载成功！！！");
    }

    public static void main(String[] args) {
        ThreadDownloader threadDownloader1 = new ThreadDownloader("http://img.netbian.com/file/2020/0904/9b933abf1608906132ef3d924910ae9e.jpg","e:/Test/downloader1.jpg");
        ThreadDownloader threadDownloader2 = new ThreadDownloader("http://img.netbian.com/file/2021/1029/77cdb4ee797bba3bd38ad26042d0c204.jpg","e:/Test/downloader2.jpg");
        ThreadDownloader threadDownloader3 = new ThreadDownloader("http://img.netbian.com/file/2017/0102/221fa6a86202e7be9c7aa6f03daf56f3.jpg","e:/Test/downloader3.jpg");

        threadDownloader1.start();
        threadDownloader2.start();
        threadDownloader3.start();
    }
}

//下载图片
class WebDownloader{
    public void Downloader(String url,String filename){
        try {
            FileUtils.copyURLToFile(new URL(url),new File(filename));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

##### 控制台：

![image-20211030221118855](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211030221118855.png)

### 实现Runnable接口

#### 示例：

```java
public class Runnable_ {
    public static void main(String[] args) {
        //创建Runnable接口实现类的对象
        TestRunnable testRunnable = new TestRunnable();
        //创建线程对象
        Thread thread = new Thread(testRunnable);
        //开启线程
        thread.start();

        for (int i = 0 ; i <= 100 ; i++){
            System.out.println("main方法==="+i);
        }
    }
}

//定义一个线程类，实现Runnable接口
class TestRunnable implements Runnable{

    //run 方法中编写业务代码
    @Override
    public void run() {
        for (int i = 0 ; i <= 100 ; i++){
            System.out.println("TestRunnable线程==="+i);
        }
    }
}
```

#### 运行效果

![image-20211030233157312](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211030233157312.png)

### 实现Callable接口

#### 示例（修改下载图片的案例）：

```java
//实现Callable接口
public class TestCallable implements Callable<Boolean> {

    private String url;
    private String fileName;

    public TestCallable(String url,String filename){
        this.url = url;
        this.fileName = filename;
    }

    //重写 call() 方法，相当于 run()
    @Override
    public Boolean call() throws Exception {
        WebDownloader2 webDownloader = new WebDownloader2();
        webDownloader.Downloader(url,fileName);
        System.out.println(fileName+"文件下载成功！！！");
        return true;
    }

    public static void main(String[] args) {
        TestCallable testCallable1 = new TestCallable("http://img.netbian.com/file/2020/0904/9b933abf1608906132ef3d924910ae9e.jpg","e:/Test/downloader1.jpg");
        TestCallable testCallable2 = new TestCallable("http://img.netbian.com/file/2021/1029/77cdb4ee797bba3bd38ad26042d0c204.jpg","e:/Test/downloader2.jpg");
        TestCallable testCallable3 = new TestCallable("http://img.netbian.com/file/2017/0102/221fa6a86202e7be9c7aa6f03daf56f3.jpg","e:/Test/downloader3.jpg");
        
        // FutureTask<Singleton> result1 = new FutureTask<Singleton>(testCallable1);
        // new Thread(task1).start();
        // FutureTask<Singleton> result2 = new FutureTask<Singleton>(testCallable2);
        // new Thread(task1).start();
        // FutureTask<Singleton> result3 = new FutureTask<Singleton>(testCallable3);
        // new Thread(task1).start();

        //创建执行服务
        ExecutorService executorService = Executors.newFixedThreadPool(3);
        //提交执行
        Future<Boolean> result1 = executorService.submit(testCallable1);
        Future<Boolean> result2 = executorService.submit(testCallable2);
        Future<Boolean> result3 = executorService.submit(testCallable3);

        try {
            //获取返回值
            Boolean r1 = result1.get();
            Boolean r2 = result2.get();
            Boolean r3 = result3.get();

            System.out.println(r1+"   "+r2+"    "+r3);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            //关闭服务
            executorService.shutdownNow();
        }
    }
}

//下载图片
class WebDownloader2{
    public void Downloader(String url,String filename){
        try {
            FileUtils.copyURLToFile(new URL(url),new File(filename));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 运行效果

![image-20211031142518545](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211031142518545.png)

## Lamda 表达式

`λ`希娜字母，英语名称 lamda

属于函数式编程的概念

作用：

> 避免内部类定义过多
>
> 简化代码

### 函数式接口

理解Functional Interface（函数式接口）是学习 lamda 表达式的关键所在

#### 定义：

任何一个接口，如果只包含唯一一个抽象方法，那么他就是一个函数式接口

```java
public interface Runnable{
    public abstract void run();
}
```

对于函数式接口，我们可以通过 lamda 表达式来创建该接口的对象

#### Lamda表达式的简化推导：

```java
public class Lamda_ {

    //2、静态内部类
    static class ILike2 implements Like{
        @Override
        public void lamda() {
            System.out.println("i like2");
        }
    }

    public static void main(String[] args) {
        Like like = new ILike();
        like.lamda();

        like = new ILike2();
        like.lamda();

        //3、局部内部类
        class ILike3 implements Like{
            @Override
            public void lamda() {
                System.out.println("i like3");
            }
        }

        like = new ILike3();
        like.lamda();

        //4、匿名内部类
        like = new Like() {
            @Override
            public void lamda() {
                System.out.println("i like4");
            }
        };
        like.lamda();

        //5、lamda表达式
        like = () -> {
            System.out.println("i like5");
        };
        like.lamda();

    }
}

//函数式接口
interface Like{
    public void lamda();
}

//1、接口实现类
class ILike implements Like{
    @Override
    public void lamda() {
        System.out.println("i like");
    }
}
```

#### lamda表达式的使用：

```java
public class Lamda2_ {

    public static void main(String[] args) {
        Love love = (int a)->{
            System.out.println("i love you"+a);
        };
        love.ILove(100);

        love = (a)->{
            System.out.println("i love you"+a);
        };
        love.ILove(200);

        //只有一个参数时可以省略括号
        love = a -> {
            System.out.println("i love you"+a);
        };
        love.ILove(300);

        //当花括号中只有一行代码时，可以省略花括号
        love = a -> System.out.println("i love you"+a);
        love.ILove(400);
    }
}

interface Love{
    public void ILove(int a);
}
```

## 静态代理模式

真实对象和代理对象要实现同一个接口

代理对象要代理真实对象

好处：

> 代理对象可以做真实对象做不了的事
>
> 真实对象专注做自己的事情

### 示例：

```java
package com.zym;

public class Proxy_ {
    public static void main(String[] args) {
        /**
         * 静态代理模式：
         * Runnable 接口相当于 Marry 接口
         * Thread 类相当于代理对象（wedding）
         * 传入的 Runnable 实例相当于真实对象
         * 真实对象由我们定义，代理对象帮助我们开辟线程需要的栈空间，栈空间开辟成功后代理对象会调用自定义的真实对象（run 方法）
         */
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("hi");
            }
        });
        thread.start();

        new Thread(()-> System.out.println("hello")).start();

        new wedding(()-> System.out.println("my marry")).youMarry();

    }
}

//接口
interface Marry{
    public void youMarry();
}

//真实对象
class You implements Marry {
    public void youMarry(){
        System.out.println("you marry");
    }
}

//代理对象
class wedding implements Marry{

    private Marry you;

    public wedding(Marry you) {
        this.you = you;
    }

    @Override
    public void youMarry() {
        one();
        you.youMarry();
        tow();
    }

    private void tow() {
        System.out.println("第一步");
    }

    private void one() {
        System.out.println("最后一步");
    }
}

```

## 线程的生命周期

![image-20211031160834445](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211031160834445.png)

## 线程的常用方法

| 方法                         | 作用                                                 |
| ---------------------------- | ---------------------------------------------------- |
| setPriority(int newPriority) | 更改线程的优先级。                                   |
| sleep(long millis)           | 在指定的毫秒数内让当前正在执行的线程休眠（暂停执行） |
| join()                       | 等待该线程终止。                                     |
| yield()                      | 暂停当前正在执行的线程对象，并执行其他线程。         |
| interrupt()                  | 中断线程。（最好别用）                               |
| isAlive()                    | 测试线程是否处于活动状态。                           |

### 线程停止

不建议强制停止线程，如果不得不强制停止的话推荐示使用自定义标记法中断线程

```java
public class Stop_ implements Runnable {

    //设置一个标志位
    private boolean flag = true;

    @Override
    public void run() {
        int i = 0;
        while (flag){
            System.out.println("run Thread"+i++);
        }
    }
    
    //修改线程停止标志
    public void stop(){
        this.flag=false;
    }

    public static void main(String[] args) {
        Stop_ stop = new Stop_();
        new Thread(stop).start();
        for (int i = 0; i < 1000; i++) {
            if (i == 900){
                //停止线程
                stop.stop();
                System.out.println("线程停止");
            }
            System.out.println("main"+i);
        }
    }
}
```

### 线程休眠（sleep）

sleep指定当前线程阻塞的毫秒数

每个对象都有一个锁，sleep不会释放锁

可以用来设置倒计时，模拟网络延时等

**示例：**

```java
public class TestSleep{

    public static void tenDown() throws InterruptedException {
        int num = 10;

        while (true){
            Thread.sleep(1000);
            System.out.println(num--);
            if (num <= 0){
                break;
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        tenDown();
    }
}
```

### 线程礼让（不一定成功）

让当前正在执行的线程暂停，但不阻塞

```java
public class TestYield {
    public static void main(String[] args) {
        Thread thread1 = new Thread(()-> {
                System.out.println("线程1开始执行");
                Thread.yield();
                System.out.println("线程1停止执行");
        });

        Thread thread2 = new Thread(()->{
            System.out.println("线程2开始执行");
            System.out.println("线程2停止执行");
        });

        thread1.start();
        thread2.start();
    }
}
```

### 合并线程（join）

不建议使用，相当于插队，会阻塞其他线程

```java
public class TestYield {
    public static void main(String[] args) {
        Thread thread1 = new Thread(()-> {
            for (int i = 0; i < 100; i++) {
                System.out.println("线程1-->"+i);
            }
        });

        Thread thread2 = new Thread(()->{
            for (int i = 0; i < 100; i++) {
                if (i == 30){
                    try {
                        //先执行线程，在插队
                        thread1.start();
                        thread1.join();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println("线程2-->"+i);
            }
        });

        thread2.start();
    }
}
```

### 线程状态查询（State）：

![image-20211031200638120](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211031200638120.png)

#### 示例：

```java
public class TestYield {
    public static void main(String[] args) {
        Thread thread1 = new Thread(()-> {
            for (int i = 0; i < 4; i++) {
                System.out.println("线程1-->"+i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        //获取thread1的状态
        Thread.State state = thread1.getState();
        System.out.println(state);
        //执行thread1线程
        thread1.start();
        //获取thread1的状态
        state = thread1.getState();
        System.out.println(state);
        while (state != Thread.State.TERMINATED){
            try {
                //获取thread1的状态
                state = thread1.getState();
                Thread.sleep(800);
                System.out.println(state);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 运行效果：

![image-20211031201641707](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211031201641707.png)

## 线程优先级

java提供线程调度器来监控程序中启动后进入就绪状态的所有线程，献策好难过调度器按照优先级来绝决定应该调度哪个线程执行

线程优先级用数字表示，范围从 1~10， <font color="red">优先级高的线程获得的资源会多一些，但不一定会先执行</font>

通过 setPriority(int newPriority) 改变线程的优先级，getPriority() 获取线程的优先级

示例：

```java
public class Testpriority {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName() + "-->"+Thread.currentThread().getPriority());
        MyPriority myPriority = new MyPriority();
        Thread thread1 = new Thread(myPriority);
        Thread thread2 = new Thread(myPriority);
        Thread thread3 = new Thread(myPriority);
        Thread thread4 = new Thread(myPriority);

        thread4.setPriority(10);
        thread3.setPriority(8);
        thread1.start();
        thread2.start();
        thread3.start();
        thread4.start();
    }
}

class MyPriority implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "-->"+Thread.currentThread().getPriority());
    }
}
```

## 守护线程（daemon）

线程分为用户线程和守护线程（例如后台记录操作日志，监控内存，垃圾回收）

虚拟机必须确保用户线程执行完毕，不用等待守护线程执行完毕

```java
public class TestDaemon {
    public static void main(String[] args) {
        God god = new God();
        You1 you = new You1();

        /**
         * 守护线程不用手动关闭，当用户线程死亡，一段时间后，守护线程会自动关闭
         */
        Thread thread1 = new Thread(god);
        //设置thread1位守护线程，一般默认都是用户线程
        thread1.setDaemon(true);
        thread1.start();
        Thread thread2 = new Thread(you);
        thread2.start();

    }
}

//上帝
class God implements Runnable{

    @Override
    public void run() {
        while (true){
            System.out.println("上帝守护着你");
        }
    }
}

//你
class You1 implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 365; i++) {
            System.out.println("你还活着");
        }
        System.out.println("你死了");
    }
}
```

## 线程的同步机制

### 并发

#### 初识并发

##### 示例：

```java
public class TestThread_ {

    public static void main(String[] args) {
        CePiao cePiao1 = new CePiao();
        new Thread(cePiao1,"张三").start();
        new Thread(cePiao1,"李四").start();
        new Thread(cePiao1,"法外狂徒").start();
    }
}

class CePiao implements Runnable{

    private int ticketNums = 10;

    @Override
    public void run() {
        while (true){
            if (ticketNums == 0){
                break;
            }
            System.out.println(Thread.currentThread().getName()+"    拿到了第"+ticketNums--+"张票");
        }
    }
}
```

##### 运行效果

![image-20211031000119029](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211031000119029.png)

### 同步方法和同步块

#### synchronized 关键字标识的方法是同步方法

```java
//synchronized 默认锁住的是 this ，当前对象
public synchronized void run() {
        while (true){
            if (ticketNums <= 0){
                break;
            }
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName()+"    拿到了第"+ticketNums--+"张票");
        }
    }
```

#### `synchronized (Obj){}`包含的代码块是同步代码块

Obj被称为同步监视器

> Obj 可以是任何对象，但是推荐使用共享资源作为同步监视器
>
> 同步方法中无需指定同步监视器，因为同步方法监视的就是 this，即这个对象本身，或者class

同步监视器的执行过程

> 第一个线程访问，锁定同步监视器，执行其中代码
>
> 第二个线程访问，发现同步监视器被锁定，无法访问
>
> 第一个线程执行完毕，解锁同步监视器
>
> 第二个线程访问，发现同步监视器没有锁，当即锁定并访问

```java
    //取钱
    public void get(){
        //需要修改的是accont就锁accont
        synchronized (accont){
        if (accont.getMoney() >= monney){
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            int mon = accont.getMoney();
            accont.setMoney(mon - monney);
            System.out.println(this.getName()+"   "+name+"取走:"+monney+"   余额："+accont.getMoney());
        }else {
            System.out.println(this.getName()+"   "+"余额不足"+"   余额："+accont.getMoney());
            return;
        }
        }
    }
```

**线程安全的集合：CopyOnWriteArrayList**

### 队列和锁

> 由于一个进程的多个线程共享同一块存储空间，会有访问冲突问题，为了保证数据在方法中被访问时的正确性，在访问时加入锁机制（synchronized）
>
> 一个线程持有锁会导致其他所有需要此锁的线程挂起
>
> 在多线程竞争下，加锁、释放锁会导致较多的上下文切换 和 调度延时，引起性能问题
>
> 如果一个优先级高的线程等待一个优先级低的线程释放锁，会导致优先级的倒置，引起性能问题

#### 死锁

多个线程各自占有一些共享资源，并相互等待其他程序占有的资源才能运行，而导致两个或多个线程都在等待对方释放资源，从而都停止执行的情形，某个同步块拥 “两个以上对象的锁” 时，就会发生死锁问题

##### 产生死锁的必要条件

> 互斥条件：一个资源每次只能被一个线程使用
>
> 请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放
>
> 不剥夺条件：线程获得的资源，在为使用完之前，不能强行剥夺
>
> 循环等待条件：若干线程之间形成一种循环等待的资源关系

##### 例子

```java
public class DeadLock {

    //死锁：多个线程互相拥有对方所需要的资源，然后形成僵持
    public static void main(String[] args) {
        Makeup makeup1 = new Makeup(0,"我恁爹");
        Makeup makeup2 = new Makeup(1,"yyb");

        makeup1.start();
        makeup2.start();
    }
}

class Lipstick { }

class Mirror { }

class Makeup extends Thread{

    static Lipstick lipstick = new Lipstick();
    static Mirror mirror = new Mirror();

    int choice;     //选择
    String girName;

    public Makeup(int choice,String girName){
        this.choice=choice;
        this.girName=girName;
    }

    @Override
    public void run() {
        makeup();
    }

    //需要拿到对方的资源
    private void makeup(){
        if (choice == 0){
            //获得Lipstick的锁
            synchronized (lipstick){
                System.out.println(this.girName+"获得Lipatick");
                try {
                    Thread.sleep(1000);
                    //一秒钟后想获得mirror
                    synchronized (mirror){
                        System.out.println(this.girName+"获得mirror");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }else {
            //获得mirror的锁
            synchronized (mirror){
                System.out.println(this.girName+"获得mirror");
                try {
                    Thread.sleep(2000);
                    //一秒钟后想获得Lipatick
                    synchronized (lipstick){
                        System.out.println(this.girName+"获得Lipatick");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### Lock锁

> JDK5.0提供的线程同步机制，通过显示定义同步锁对象来实现同步，同步锁使用Lock来存档对象。
>
> Lock接口时控制对象对共享资源访问的工具，提供了对共享资源的独占访问，每次只能有一个对象对Lock对象加锁，线程开始访问共享资源之前应该先获得Lock对象。
>
> ReentrantLock（可重入锁）类实现了Lock，它拥有与synchronized相同的并发性和内存语义，在实现线程安全的控制中，比较常见的是ReentrantLock，可显式加锁，释放锁。

#### 示例

```java
public class TestLock {
    public static void main(String[] args) {
        Cepiao cePiao = new Cepiao();
        new Thread(cePiao,"张三").start();
        new Thread(cePiao,"李四").start();
        new Thread(cePiao,"法外狂徒").start();
    }
}

class Cepiao implements Runnable{

    int tickNum = 10;
    private final ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true){
            try {
                lock.lock(); //加锁
                if (tickNum > 0){
                    System.out.println(Thread.currentThread().getName()+"抢到了第"+tickNum--+"张票");
                }else {
                    break;
                }
            }catch (Exception e){
                e.printStackTrace();
            }finally {
                lock.unlock(); //解锁
            }
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 线程协作

### 生产者与消费者问题

生产者生产出一个商品供消费者使用，消费者使用之后需要通知生产者生产下一个商品。

此时，仅仅靠synchronized关键字是不够的

> synchronized 关键字只能阻止并发更新同一个资源，实现同步
>
> synchronized 关键字不能够实现不同线程之间的信息传递（通信）

### Java提供的解决线程之间通信的方法

以下均是Object类的方法，都只能在同步方法或者同步代码块中使用，否则会抛出异常`IllegalMonitorStateException`

|       方法名       |                            作用                            |
| :----------------: | :--------------------------------------------------------: |
|       wait()       |        表示线程一直等待，直到其他线程通知，会释放锁        |
| wait(long timeout) |            与wait()相似，但只会等待timeout毫秒             |
|      notify()      |                 唤醒一个处于等待状态的线程                 |
|    notifyAll()     | 唤醒同一对象的所有wait()方法的线程，优先级高的线程优先调度 |

#### 方法一：管程法

> 生产者：负责生产数据的模块
>
> 消费者：负责处理数据的模块
>
> 缓冲区：消费者不能直接使用生产者的数据，他们之间有一个缓冲区

生产者将生产好的数据放入缓冲区中，消费者在缓冲区中拿出数据

示例：

```java
/**
 * 测试生产者消费者模型-----》管程法
 */
public class TestPC {
    public static void main(String[] args) {
        SynContainer synContainer = new SynContainer();
        new Thread(new Productor(synContainer)).start();
        new Thread(new Consumer(synContainer)).start();
    }
}

/**
 * 生产者
 */
class Productor implements Runnable{
    /**
     * 缓冲区
     */
    SynContainer synContainer;

    public Productor(SynContainer synContainer) {
        this.synContainer = synContainer;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("生产了"+i+"只鸡");
            synContainer.push(new Chicken(i));
        }
    }
}

/**
 * 消费者
 */
class Consumer implements Runnable{
    /**
     * 缓冲区
     */
    SynContainer synContainer;

    public Consumer(SynContainer synContainer){
        this.synContainer = synContainer;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("消费了"+synContainer.pop().id+"只鸡");
        }
    }
}

/**
 * 产品
 */
class Chicken {
    /**
     * 产品编号
     */
    int id;

    public Chicken(int id){
        this.id = id;
    }
}

/**
 * 缓冲区
 */
class SynContainer {
    /**
     * 需要一个容器大小 chickens
     * 容器计数器 count
     */
    Chicken[] chickens = new Chicken[10];
    int count = 0;

    /**
     * 生产者放入产品
     */
    public synchronized void push(Chicken chicken){
        //如果容器满了就需要等待消费者消费
        if (count == chickens.length){
            //生产者等待，通知消费者消费
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //如果没有满就放入产
        chickens[count] = chicken;
        count++;
        this.notifyAll();
    }
    
    /**
     * 消费者消费产品
     */
    public synchronized Chicken pop(){
        //判断能否消费
        if (count == 0){
            //消费者等待，通知生产者生产
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //可以消费
        count--;
        Chicken chicken = chickens[count];

        this.notifyAll();
        return chicken;
    }
}

```

#### 方法二：信号灯法（标志位解决）

```java
/**
 * 测试生产者消费者问题的第二种方法：信号灯法（通过标志位解决）
 */
public class TestPC2 {
    public static void main(String[] args) {
        TV tv = new TV();
        new Thread(new Player(tv)).start();
        new Thread(new Watcher(tv)).start();
    }
}

/**
 * 生产者：演员
 */
 class Player implements Runnable{
    TV tv;
    public Player(TV tv){
        this.tv = tv;
    }
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            if (i%2==0){
                this.tv.play("快乐大本营播放");
            }else{
                tv.play("广告");
            }
        }
    }
}

/**
 * 消费者：观众
 */
 class Watcher implements Runnable{
     TV tv;
     public Watcher(TV tv){
         this.tv = tv;
     }
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            tv.watch();
        }
    }
}

/**
 * 产品：节目
 */
class TV{
    /**
     * 演员表压，观众等待  T
     * 观众观看，演员等待  F
     * voice 表演的节目
     */
    String voice;
    boolean flag = true;

    /**
     * 表演
     */
    public synchronized void play(String voice){
        if (!flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("演员表演了："+voice);
        //通知观众观看
        this.notifyAll();
        this.voice = voice;
        this.flag = !this.flag;
    }

    /**
     * 观看
     */
    public synchronized void watch(){
        if (flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("观众观看了："+voice);
        this.notifyAll();
        this.flag = !this.flag;
    }
}
```

## 线程池

经常创建和销毁线程对资源的使用量很大，很影响性能

使用线程池，提前创建好多个线程，放在线程池中，使用时直接获取，使用完放回线程池中，可以避免频繁的创建和销毁。

好处：

> 提高响应速度（减少了创建线程需要的时间）
>
> 降低资源的消耗（重复利用，没需要每次都创建）
>
> 便于管理

线程池相关API：ExecutorService和Executors

> ExecutorService：线程池接口
>
> | `<T> Future<T>` | submit(Callable<T> task)   提交一个返回值的任务用于执行，返回一个表示任务的未决结果的 Future。 |
> | --------------- | ------------------------------------------------------------ |
> | ` void`         | shutdown()      启动一次顺序关闭，执行以前提交的任务，但不接受新任务。 |
> | `void`          | execute(Runnable command)    在未来某个时间执行给定的命令。  |
>
> Executors：工具类，线程池工厂，用于创建并返回不同类型的线程池

```java
package com.zymimport java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class TestPool {
    public static void main(String[] args) {
        //创建线程池
        ExecutorService service = Executors.newFixedThreadPool(10);
        service.execute(new MyThrad());
        service.execute(new MyThrad());
        service.execute(new MyThrad());
        service.execute(new MyThrad());
        service.execute(new MyThrad());
        //关闭连接
        service.shutdown();
    }
}

class MyThrad implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

