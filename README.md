# java-obj-diff
比较对象差异工具类。主要用于比较同一个类的两个对象属性值的差异。

此工具是建立在
```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```
中的DiffBuilder基础上进行的重新开发和封装。
主要是解决一些bug.和加入了一些缓存提高比较时频繁反射的性能。同时增加了一些新特性。

### 特性
#### 属性忽略比较
##### 注解支持
使用 `@DiffIgnore` 注解可以忽略某个属性的比较。

```java
@DiffIgnore
private String name;
```

##### 手动忽略
调用工具类DiffUtil 时传递排除比较的字段名。

```java
DiffResult diffResult = DiffUtil.diff(obj1, obj2, "name");
```

#### 自定义比较器
使用 `@DiffCompare` 注解可以自定义某个属性的比较器。
自定义比较器需要实现 `DiffComparable` 接口。

```java
public class MyComparator implements DiffComparable {
    @Override
    public boolean diff(Object obj1, Object obj2) {
        return obj1.equals(obj2);
    }
}
```

使用自定义比较器
```java
@DiffCompare(using = MyComparator.class)
private String name;
```

#### 自定义类型属性
支持自定义成员变量需要使用注解标识。会自动解析到属性
可以加注解`@DiffBean`来标识。

```java
public class User {
    private String name;
    private int age;
    private Date birthday;
    @DiffBean
    private Address address;

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

    public Date getBirthday() {
        return birthday;
    }
    
    public void setAddress(Address address) {
        this.address = address;
    }

    public Address getAddress() {
        return this.address;
    }
}
```

#### 别名支持
使用 `@DiffAlias` 注解可以自定义某个属性的别名。

```java
@DiffAlias(alias = "用户名")
private String name;
```
输出报告中会显示别名。否则默认使用字段属性名。

#### 格式化支持
使用 `@DiffFormat` 注解可以自定义某个属性的格式化。在输出结果中展示格式化后的数据。

```java
@DiffFormat(pattern = "yyyy-MM-dd",using=DateTimeDiffFormatter.class)
private Date birthday;
```

也可以自定义格式化器。需要实现接口:`com.openquartz.javaobjdiff.DiffFormatter`

```java
public class DateTimeDiffFormatter implements DiffFormatter {
    @Override
    public String format(Object obj) {
        return DateTimeFormatter.ofPattern("yyyy-MM-dd").format((Date) obj);
    }
}
```
