## java基础

#### 1.字符串截取

​    截取某个字符之前或之后的数据

```java
	String str = lly://enterVideoList?result={jsonString};

    截取?之前字符串
    String str1=str.substring(0, str.indexOf("?"));
    截取?之后字符串
    String str1=str.substring(0, str.indexOf("?"));
    String str2=str.substring(str1.length()+1, str.length());
```



取出正数第二个"."后面的内容



```java
public class TestCode {
    public static void main(String[] args) {
        String str ="232ljsfsf.sdfl23.ljsdfsdfsdfss.23423.sdfsdfsfd";
        //获得第一个点的位置
        int index=str.indexOf(".");
        System.out.println(index);
        //根据第一个点的位置 获得第二个点的位置
        index=str.indexOf(".", index+1);
        //根据第二个点的位置，截取 字符串。得到结果 result
        String result=str.substring(index);
        //输出结果
        System.out.println(result);
    }

}
```

#### 2.日期相减

```java
	   String dateStart = "2013-02-19 09:29:58";
        String dateStop = "2013-02-20 11:31:48";

        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

        Date d1 = null;
        Date d2 = null;

        try {
            d1 = format.parse(dateStart);
            d2 = format.parse(dateStop);

            //毫秒ms
            long diff = d2.getTime() - d1.getTime();

            long diffSeconds = diff / 1000 % 60;
            long diffMinutes = diff / (60 * 1000) % 60;
            long diffHours = diff / (60 * 60 * 1000) % 24;
            long diffDays = diff / (24 * 60 * 60 * 1000);

            System.out.print("两个时间相差：");
            System.out.print(diffDays + " 天, ");
            System.out.print(diffHours + " 小时, ");
            System.out.print(diffMinutes + " 分钟, ");
            System.out.print(diffSeconds + " 秒.");

        } catch (Exception e) {
            e.printStackTrace();
        }
```

#### 3.if else优化

代码中如果if-else比较多，阅读起来比较困难，维护起来也比较困难，很容易出bug，接下来，本文将介绍优化if-else代码的八种方案。

##### 1：提前return，去除不必要的else

如果if-else代码块包含return语句，可以考虑通过提前return，把多余else干掉，使代码更加优雅。

优化前：

```java
if(condition){    
   //doSomething 
}else{    
    return; 
}
```

优化后：

```java
if（!condition）{ 
    return ; 
} 
//doSomething
```

##### 2.使用三目运算符

使用条件三目运算符可以简化某些if-else,使代码更加简洁，更具有可读性。

优化前：

```java
int  price ; 
if(condition){    
    price = 80; 
}else{    
    price = 100; 
}
```

优化 后：

```java
int price = condition?80:100;
```

##### 3.使用枚举

在某些时候，使用枚举也可以优化if-else逻辑分支，按个人理解，它也可以看做一种**表驱动方法**。

优化前：

```java
String OrderStatusDes; 
if(orderStatus==0){    
	OrderStatusDes ="订单未支付"; 
}else if(OrderStatus==1){    
    OrderStatusDes ="订单已支付"; 
}else if(OrderStatus==2){   
    OrderStatusDes ="已发货";  
} 
...
```

优化后：

先定义一个枚举

```java
public enum OrderStatusEnum {   
    
    UN_PAID(0,"订单未支付"),
    
    PAIDED(1,"订单已支付"),
    
    SENDED(2,"已发货");  
    
    private int index;    
    private String desc;   
    
    public int getIndex() {        
        return index;    
    }
    
    public String getDesc() {        
        return desc;    
    }     
    
    OrderStatusEnum(int index, String desc){        
        this.index = index;        
        this.desc =desc;    
    }     
    
    OrderStatusEnum of(int orderStatus) {        
        for (OrderStatusEnum temp : OrderStatusEnum.values()) {            
            if (temp.getIndex() == orderStatus) {                
                return temp;            
            }        
   }        
        return null;    
    } 
}
```

有了枚举之后，以上if-else逻辑分支，可以优化为一行代码

```java
String OrderStatusDes = OrderStatusEnum.0f(orderStatus).getDesc();
```

##### 4.使用 Optional

有时候if-else比较多，是因为非空判断导致的，这时候你可以使用java8的Optional进行优化。



优化前：

```java
String str = "jay@huaxiao";
if (str != null) {    
    System.out.println(str); 
} else {    
    System.out.println("Null"); 
}
```

优化后：

```java
Optional<String> strOptional = Optional.of("jay@huaxiao"); strOptional.ifPresentOrElse(System.out::println, () -> System.out.println("Null"));
```

##### 5.表驱动法

**驱动法**，又称之为表驱动、表驱动方法。表驱动方法是一种使你可以在表中查找信息，而不必用很多的逻辑语句（if或Case）来把它们找出来的方法。以下的demo，把map抽象成表，在map中查找信息，而省去不必要的逻辑语句。

优化前：

```java
if (param.equals(value1)) {    
    doAction1(someParams); 
} else if (param.equals(value2)) {    
    doAction2(someParams); 
} else if (param.equals(value3)) {    
    doAction3(someParams); 
} 
// ...
```

优化后：

```java
Map<?, Function<?> action> actionMappings = new HashMap<>(); 
// 这里泛型 ? 是为方便演示，实际可替换为你需要的类型 
// 初始化 
actionMappings.put(value1, (someParams) -> { doAction1(someParams)}); 
actionMappings.put(value2, (someParams) -> { doAction2(someParams)}); 
actionMappings.put(value3, (someParams) -> { doAction3(someParams)}); 
// 省略多余逻辑语句
actionMappings.get(param).apply(someParams);
```

##### 6.优化逻辑结构，让正常流程走主干

优化前：

```java
public double getAdjustedCapital(){    
    
    if(_capital <= 0.0 ){        
        return 0.0;    
    }    
    if(_intRate > 0 && _duration >0){        
        return (_income / _duration) *ADJ_FACTOR;    
    }    
    	return 0.0; 
}
```

优化后：

```java
public double getAdjustedCapital(){    
    if(_capital <= 0.0 ){       
        return 0.0;    }    
    if(_intRate <= 0 || _duration <= 0){        
        return 0.0;    
    }     
    return (_income / _duration) *ADJ_FACTOR; 
}
```

将条件反转使异常情况先退出，让正常流程维持在主干流程，可以让代码结构更加清晰。

##### 7.策略模式+工程方法消除if else

假设需求为，根据不同勋章类型，处理相对应的勋章服务，优化前有以下代码

```java
String medalType = "guest";    
if ("guest".equals(medalType)) {        
    System.out.println("嘉宾勋章");     
} else if ("vip".equals(medalType)) {        
    System.out.println("会员勋章");    
} else if ("guard".equals(medalType)) {        
    System.out.println("展示守护勋章");    
}   
...
```

首先，我们把每个条件逻辑代码块，抽象成一个公共的接口，可以得出以下代码：

```java
//勋章接口 
public interface IMedalService {    
    void showMedal();
}
```

我们根据每个逻辑条件，定义相对应的策略实现类，可得以下代码：

```java
//守护勋章策略实现类 
public class GuardMedalServiceImpl implements IMedalService {    
    @Override    
    public void showMedal() {        
        System.out.println("展示守护勋章");    
    } 
} 
//嘉宾勋章策略实现类 
public class GuestMedalServiceImpl implements IMedalService {    
    @Override    
    public void showMedal() {        
        System.out.println("嘉宾勋章");    
    } 
} 
//VIP勋章策略实现类 
public class VipMedalServiceImpl implements IMedalService {    
    @Override    
    public void showMedal() {        
        System.out.println("会员勋章");    
    } 
}
```

接下来，我们再定义策略工厂类，用来管理这些勋章实现策略类，如下：



```java
//勋章服务工产类
public class MedalServicesFactory {     
    private static final Map<String, IMedalService> map = new HashMap<>();    
    static {       
        map.put("guard", new GuardMedalServiceImpl());        
        map.put("vip", new VipMedalServiceImpl());       
        map.put("guest", new GuestMedalServiceImpl());    
    }    
    public static IMedalService getMedalService(String medalType) {        
        return map.get(medalType);    
    } 
}
```

使用了策略+工厂模式之后，代码变得简洁多了，如下：

```java
public class Test {    
    public static void main(String[] args) {       
        String medalType = "guest";        
        IMedalService medalService = MedalServicesFactory.getMedalService(medalType);        		             medalService.showMedal();    
    } 
}
```

