#Java 8的重要语言特性

## Lambda

### 写法
- (参数) -> 表达式
- (参数) -> { 语句; }

### Lambda代替接口的实例
new Thread(() -> { System.out.println("Hello Spring"); }).start();


## Stream
### 常用操作
- forEach
- filter
- map
- flatMap