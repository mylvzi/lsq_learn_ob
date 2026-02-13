> 来自于孙高飞 https://testerhome.com/topics/15540

# 总体规则
> 采用**Page Object**的思想进行设计, 和设计后端代码的方式一样(dao + controller + service), 是UI自动化设计的核心思想

具体来说分为三层
![[Pasted image 20250714215829.png]]

* Page层:一个页面封装为一个类, 在这个类中只能写和本页面相关的业务逻辑,不能跨页面!业务逻辑指的是与本页面相关的操作和控件(注意只封装逻辑,不含有具体的参数,参数是要通过用例层提供)
* 用例层:测试人员设计的多个测试用例,在用例层中调用Page层/封装层的中的逻辑/操作(提供具体的测试用例/测试参数)
* 封装层:主要是封装**跨页面的联动**相关逻辑,Page层中只包含当前页面的操作/逻辑,但实际的项目中往往是多个页面联动的, 封装层中就负责这种多页面交互的逻辑

# 二.实战讲解
> 下面以**登录 + 下单**讲解

## 前期准备:创建maven项目,提供三个包
![[Pasted image 20250714220745.png]]
* flow:封装层
* page:页面层
* test:用例层
## 1.Page层设计
1. 登录页面
2. 下单页面
![[Pasted image 20250714220954.png]]
**Page层下的类必须以Page结尾**

```java
public class LoginPage {  
    /**  
     * 输入用户名  
     * @param username  
     */  
    public void inputUsername(String username) {  
        // input username  
  
    }  
  
    /**  
     * 输入密码  
     * @param password  
     */  
    public void inputPassword(String password) {  
        // input password  
    }  
  
    /**  
     * 点击登录  
     */  
    public void clickLoginButton() {  
        // click login button  
    }  
}

public class OrderPage {  
    /**  
     * 添加商品到购物车  
     */  
    public void addProductToCart() {  
        // add product to cart  
    }  
  
    /**  
     * 查询订单  
     */  
    public void clickQueryButton() {  
        // click query button  
    }  
}
```

## 封装层设计
```java
/**  
 * 登录并下单流程  
 */  
public class OrderFLow {  
    public void loginAndOrder(String username, String password) {  
        // login and order  
        LoginPage loginPage = new LoginPage();  
        loginPage.inputUsername(username);  
        loginPage.inputPassword(password);  
        loginPage.clickLoginButton();  
  
        OrderPage orderPage = new OrderPage();  
        orderPage.addProductToCart();  
        orderPage.clickQueryButton();  
    }  
}
```
**某一个流程的类名使用Flow结尾,比如下单流程就是OrderFlow**

## 用例层设计
> 用例层就是利用设计好的测试用例去执行对应的操作逻辑
> 对于登录+下单这个流程来说,可以针对各个页面设计很多的测试用例

```java
/**  
 * 测试登录 + 订单 功能  
 */  
public class TestOrder {  
    /**  
     * 测试用户可以登录并下单  
     */  
    public void testLoginAndOrderSuccess() {  
        // 测试用户可以登录并下单  
        OrderFLow orderFLow = new OrderFLow();  
        orderFLow.loginAndOrder("lvzi", "123456");  
    }  
  
    /**  
     * 测试当用户名为空时, 无法登录并下单  
     */  
    public void testLoginAndOrderFail1() {  
        // 测试用户不可以登录并下单  
        OrderFLow orderFLow = new OrderFLow();  
        orderFLow.loginAndOrder("", "123456");  
    }  
// ...... 其他测试用例  这个类下存放所有设计的与登录+下单流程相关的测试用例
// 包含正常流程和异常流程
}
```


# 三.优化
> 军规:**所有页面逻辑皆返回特定页面对象，以保证测试用例使用 workflow 式 API。**
> 一个完整的workFlow:
> 进入登录页--输入用户名和密码--点击登录按钮跳转到商品页面--选择商品--将商品加入到购物车--下单

优化:
page层
```java
public class LoginPage {  
    /**  
     * 输入用户名  
     * @param username  
     * @return LoginPage  
     */    public LoginPage inputUsername(String username) {  
        // input username  
        return this;  
    }  
  
    /**  
     * 输入密码  
     * @param password  
     * @return LoginPage  
     */    public LoginPage inputPassword(String password) {  
        // input password  
        return this;  
    }  
  
    /**  
     * 点击登录  
     * @return OrderPage  
     */    public OrderPage clickLoginButton() {  
        // click login button  
        return new OrderPage();// 登录之后要跳转到商品页面  
    }  
}

public class OrderPage {  
    /**  
     * 选择商品  
     * @return OrderPage  
     */    public OrderPage selectProduct(String productName) {  
        // select product  
        return this;  
    }  
    /**  
     * 添加商品到购物车  
     * @return OrderPage  
     */    public OrderPage addProductToCart() {  
        // add product to cart  
        return this;  
    }  
  
    /**  
     * 查询订单  
     * @return boolean  
     */    public boolean clickQueryButton() {  
        // click query button  根据具体的业务逻辑来判断是否查询成功  
        return true;  
    }  
}
```


flow层:可以封装一些更加公用的逻辑
```java
public class OrderFLow {  
    /**  
     * 以管理员身份登录  
     * @return OrderPage  
     */    public OrderPage loginAsAdmin() {  
        // login as admin  
        return new LoginPage().inputUsername("admin")  
                                .inputPassword("123456")  
                                .clickLoginButton();  
    }  
    /**  
     * 以管理员身份登录并下单  
     * @return boolean  
     */    public boolean loginAndOrder(String productName) {  
        // login and order  
        return loginAsAdmin()  
                .selectProduct(productName)  
                .addProductToCart()  
                .clickQueryButton();  
    }  
}
```

test层
```java
/**  
 * 测试登录 + 订单 功能  
 */  
public class TestOrder {  
    /**  
     * 测试管理员用户可以登录并下单  
     */  
    public boolean testLoginAndOrderSuccess() {  
        return new OrderFLow().loginAndOrder("小米手机");  
    }  
}
```


> 除特别简单的逻辑外。所有业务逻辑的参数均使用 java bean 以及枚举封装，禁止使用基本数据类型 (int,String, long 等)，并按照 UI 实际情况设计默认值

假如我现在要新增一个校验数据captcha
![[Pasted image 20250715114828.png]]

传统的写法会把调用该方法的所有参数的签名都修改掉

使用Java Bean只需要在代码中添加一行即可, 不报错,也无需修改方法签名
![[Pasted image 20250715114946.png]]

**所有与业务逻辑相关的参数都要封装为Java Bean对象或者枚举类,这样当UI层面发生改动时,我们无需对每一个方法签名进行修改,只需要添加对应的参数即可**

对于**登录 + 下单**这个业务来说,涉及到的业务逻辑参数有两个:
1. 登录信息
2. 产品信息
将其封装为两个Param类,统一放到Param包下
![[Pasted image 20250715131838.png]]

```java
@Data  
public class LoginParam {  
    private String userName;  
    private String passWord;  
    private String captcha;  
}

@Data  
public class ProductParam {  
    private String productName;  
    private String productPrice;  
    private String productCount;  
}
```

[[Java Bean]]

那么对应的page,test,flow层都需要改变
```java
public class LoginPage {  
    private WebDriver driver;  
    public LoginPage(WebDriver driver) {  
        this.driver = driver;  
    }  
  
    public LoginPage fillLoingInfo(LoginParam loginParam) {  
        driver.findElement(By.id("username")).sendKeys(loginParam.getUserName());  
        driver.findElement(By.id("password")).sendKeys(loginParam.getPassWord());  
        driver.findElement(By.id("captcha")).sendKeys(loginParam.getCaptcha());  
        driver.findElement(By.id("login")).click();  
          
        return this;  
    }
}

public class OrderPage {  
    private WebDriver driver;  
    public OrderPage(WebDriver driver) {  
        this.driver = driver;  
    }  
  
    /**  
     * 选择商品  
     * @param productParam  
     */  
    public OrderPage SelectProduct(ProductParam productParam) {  
        driver.findElement(By.id("productName")).sendKeys(productParam.getProductName());  
        driver.findElement(By.id("productPrice")).sendKeys(productParam.getProductPrice());  
        driver.findElement(By.id("productCount")).sendKeys(productParam.getProductCount());  
        return this;  
    }
```

flow
```java
public class OrderFLow {  
    private WebDriver driver;  
  
    public OrderFLow(WebDriver driver) {  
        this.driver = driver;  
    }  
  
    public void LoginAndOrder(LoginParam loginParam, ProductParam productParam) {  
        LoginPage loginPage = new LoginPage(driver);  
        loginPage.fillLoingInfo(loginParam);  
  
        OrderPage orderPage = new OrderPage(driver);  
        orderPage.SelectProduct(productParam);  
        orderPage.addProductToCart();  
  
        // 此处需要补充校验逻辑  比如通过断言来判断是否下单成功  
        assert true;  
    }  
}
```

test
```java
/**  
 * 测试登录 + 订单 功能  
 */  
public class TestOrder {  
    private WebDriver driver;  
  
    @BeforeEach  
    public void setUp() {  
        driver = new ChromeDriver();  
        driver.get("http://localhost:8080");  
    }  
  
    @AfterEach  
    public void tearDown() {  
        driver.quit();  
    }  
  
    /**  
     * 测试管理员用户可以登录并下单  
     * 在方法中传入具体的测试用例参数
     */  
  
    @Test  
    public void testLoginAndOrderSuccess() {  
        LoginParam loginParam = new LoginParam();  
        loginParam.setUserName("admin");  
        loginParam.setPassWord("admin");  
        loginParam.setCaptcha("1234");  
  
        ProductParam productParam = new ProductParam();  
        productParam.setProductName("iPhone");  
        productParam.setProductPrice("1000");  
        productParam.setProductCount("1");  
  
        new OrderFLow(driver).LoginAndOrder(loginParam, productParam);  
    }  
}
```

tip:这里发现在Junit框架下没被@Test注解标记的函数的返回值只能是**void**
这和Junit的设计哲学相关, 对于一个测试场景来说,我只关注你这次测试成功与否,测试成功与否是应该你在代码里通过断言/抛异常的方式提供,我并不关注你返回值是什么

从技术角度看,方法的返回值没有存在的必要
```java
Method method = ... // 通过反射获取 @Test 标记的方法
method.invoke(testInstance); // 调用测试方法
```
> invoke(调用/引用)会把返回值丢弃,所以你提供返回值也没有必要