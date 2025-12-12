
# The 6 Most Common Design Patterns in Java Projects (With Examples)

![](https://miro.medium.com/v2/resize:fit:870/1*pcq7_m9nRqPwF5Nwxan5Ag.jpeg)

> My article is open to everyone; non-member readers can click this [link](https://medium.com/@haiou-a/the-6-most-common-design-patterns-in-java-projects-with-examples-e3c8b0071c01?sk=3f6895c849d1430091b1eeae67f9762a) to read the full text.

Design patterns are familiar territory for many veteran developers, yet sometimes when writing code,

we still find ourselves doing: `new Object()` followed by 8 layers of nested `if-else` statements.

I used to be the same way.

Terms like Singleton, Factory, and Observer sounded fancy, but they felt awkward to use in practice.

Later, after slowly understanding them in actual projects, I realized that using these patterns correctly can save a lot of trouble.

Below, I’m writing about a few of the most commonly used design patterns and examples in daily development.

## 1. Singleton Pattern: One Class, One Instance

### What is the Singleton Pattern?

Simply put, a Singleton ensures that a class has only one instance and provides a global access point to it.

### When to use it?

- Scenarios where resource usage needs to be controlled, such as database connection pools.
- Global configuration information.
- Loggers.

### Real-world Example

For instance, in an order system, there is a service responsible for SMS notifications.

If we create a new instance of this service every time, it not only wastes resources but might also lead to duplicate SMS sending (imagine the user experience of receiving 10 identical order confirmation texts 😂).

```java
public class SMSService {  
    // Private static instance  
    private static SMSService instance;  
      
    // Private constructor  
    private SMSService() {  
        // Initialize SMS configuration  
    }  
      
    // Public access point  
    public static synchronized SMSService getInstance() {  
        if (instance == null) {  
            instance = new SMSService();  
        }  
        return instance;  
    }  
      
    public void sendOrderConfirmation(String phoneNumber, String orderInfo) {  
        // Send order confirmation SMS  
        System.out.println("Sending order confirmation to " + phoneNumber + ": " + orderInfo);  
    }  
}
```

Usage:

```java
// Get SMS service instance  
SMSService smsService = SMSService.getInstance();  
// Send order confirmation SMS  
smsService.sendOrderConfirmation("13800138000", "Order #12345 Confirmed");
```

> **_Tip:_** _The above is the most basic Singleton implementation. In actual projects, you might use “Double-Checked Locking” or a “Static Inner Class” to optimize performance._

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*jAMkF1jpQRBkvI6h)

Photo by [Timon Studler](https://unsplash.com/@derstudi?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 2. Factory Pattern: The “Assembly Line” of Object Creation

### What is the Factory Pattern?

The Factory Pattern encapsulates the process of creating objects. The caller doesn’t need to care about the specific implementation class; they just tell the factory, “I want this type of object.”

### When to use it?

- You are unsure which specific type of object needs to be created.
- The object creation logic is complex.
- You want to hide the specific implementation classes and only expose an interface.

### Real-world Example

Suppose we are developing a payment system that needs to support multiple payment methods like WeChat Pay, Alipay, and Bank Cards. The processing logic for each method is different, but the basic flow is similar.

```java
// Payment Interface  
public interface PaymentMethod {  
    void processPayment(double amount);  
}  
​  
// WeChat Pay  
public class WeChatPay implements PaymentMethod {  
    @Override  
    public void processPayment(double amount) {  
        System.out.println("Paid " + amount + " using WeChat Pay");  
        // Specific logic for WeChat Pay  
    }  
}  
​  
// Alipay  
public class AliPay implements PaymentMethod {  
    @Override  
    public void processPayment(double amount) {  
        System.out.println("Paid " + amount + " using Alipay");  
        // Specific logic for Alipay  
    }  
}  
​  
// Bank Card Pay  
public class BankCardPay implements PaymentMethod {  
    @Override  
    public void processPayment(double amount) {  
        System.out.println("Paid " + amount + " using Bank Card");  
        // Specific logic for Bank Card Pay  
    }  
}  
​  
// Payment Factory  
public class PaymentFactory {  
    public static PaymentMethod getPaymentMethod(String type) {  
        if ("wechat".equals(type)) {  
            return new WeChatPay();  
        } else if ("alipay".equals(type)) {  
            return new AliPay();  
        } else if ("bankcard".equals(type)) {  
            return new BankCardPay();  
        }  
        throw new IllegalArgumentException("Unsupported payment method: " + type);  
    }  
}
```

Usage:

```java
// User selects a payment method  
String paymentType = "wechat"; // Could come from user selection  
double amount = 99.8; // Order amount  
​  
// Get the corresponding payment processor and process payment  
PaymentMethod payment = PaymentFactory.getPaymentMethod(paymentType);  
payment.processPayment(amount);
```

This way, when we need to add a new payment method (like Credit Card), we only need to add a new implementation class and modify the factory, without changing the code that uses the payment logic.

## 3. Strategy Pattern: A Family of Replaceable Algorithms

### What is the Strategy Pattern?

The Strategy Pattern defines a series of algorithms and encapsulates each one, making them interchangeable.

### When to use it?

- There are multiple similar ways to handle something, and you need to choose dynamically based on conditions.
- To avoid using massive `if-else` blocks.
- When algorithms might change frequently.

### Real-world Example

Discount strategies in an e-commerce system are a classic example.

We might have “Full Reduction” (spend X get Y off), “Percentage Discount,” or “Direct Reduction.” The user can choose one to apply to their order.

```java
// Discount Strategy Interface  
public interface DiscountStrategy {  
    double calculateDiscount(double totalAmount);  
}  
​  
// Full Reduction Strategy (Spend X get Y off)  
public class FullReductionStrategy implements DiscountStrategy {  
    private double threshold; // Threshold amount  
    private double reduction; // Reduction amount  
      
    public FullReductionStrategy(double threshold, double reduction) {  
        this.threshold = threshold;  
        this.reduction = reduction;  
    }  
      
    @Override  
    public double calculateDiscount(double totalAmount) {  
        if (totalAmount >= threshold) {  
            return reduction;  
        }  
        return 0;  
    }  
}  
​  
// Percentage Discount Strategy  
public class PercentageDiscountStrategy implements DiscountStrategy {  
    private double percentage; // Discount percentage  
      
    public PercentageDiscountStrategy(double percentage) {  
        this.percentage = percentage;  
    }  
      
    @Override  
    public double calculateDiscount(double totalAmount) {  
        return totalAmount * (1 - percentage);  
    }  
}  
​  
// Direct Reduction Strategy  
public class DirectReductionStrategy implements DiscountStrategy {  
    private double reduction; // Direct reduction amount  
      
    public DirectReductionStrategy(double reduction) {  
        this.reduction = reduction;  
    }  
      
    @Override  
    public double calculateDiscount(double totalAmount) {  
        return reduction;  
    }  
}  
​  
// Order Class  
public class Order {  
    private List<Item> items; // Order items  
    private DiscountStrategy discountStrategy; // Discount strategy  
      
    public Order(List<Item> items) {  
        this.items = items;  
    }  
      
    // Set discount strategy  
    public void setDiscountStrategy(DiscountStrategy discountStrategy) {  
        this.discountStrategy = discountStrategy;  
    }  
      
    // Calculate total order price  
    public double calculateTotal() {  
        double total = 0;  
        for (Item item : items) {  
            total += item.getPrice() * item.getQuantity();  
        }  
          
        // Apply discount  
        if (discountStrategy != null) {  
            double discount = discountStrategy.calculateDiscount(total);  
            total -= discount;  
        }  
          
        return total;  
    }  
}
```

Usage:

```java
// Create Order  
List<Item> items = Arrays.asList(  
    new Item("Phone", 2999, 1),  
    new Item("Case", 49, 1)  
);  
Order order = new Order(items);  
​  
// Select discount strategy based on promotion  
// Example: Spend 3000 get 300 off  
if (isFullReductionPromotion()) {  
    order.setDiscountStrategy(new FullReductionStrategy(3000, 300));  
}  
// Or: 20% off entire store  
else if (isPercentageDiscountPromotion()) {  
    order.setDiscountStrategy(new PercentageDiscountStrategy(0.2));  
}  
// Or: Direct $50 off  
else if (isDirectReductionPromotion()) {  
    order.setDiscountStrategy(new DirectReductionStrategy(50));  
}  
​  
// Calculate final price  
double finalPrice = order.calculateTotal();  
System.out.println("Final Order Price: " + finalPrice);
```

The benefit of this design is that we can add new discount strategies at any time, such as “Buy One Get One 50% Off,” without modifying the core logic of order processing.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*CCTdiT6olenjxbw7)

Photo by [lucas Favre](https://unsplash.com/@we_are_rising?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 4. Observer Pattern: Event Notification Mechanism

### What is the Observer Pattern?

The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

### When to use it?

- When a change in one object requires changing others.
- Publish-Subscribe scenarios.
- Event handling systems.

### Real-world Example

A very common example is order status change notifications.

When an order status changes, multiple related systems need to be notified, such as the inventory system, logistics system, and SMS notification service.

```java
// Observer Interface  
public interface OrderObserver {  
    void update(Order order);  
}  
​  
// Concrete Observer: Inventory System  
public class InventorySystem implements OrderObserver {  
    @Override  
    public void update(Order order) {  
        if ("PAID".equals(order.getStatus())) {  
            System.out.println("Inventory System Notified: Order #" + order.getId() + " paid. Deducting stock.");  
            // Logic to deduct stock  
        } else if ("CANCELLED".equals(order.getStatus())) {  
            System.out.println("Inventory System Notified: Order #" + order.getId() + " cancelled. Restoring stock.");  
            // Logic to restore stock  
        }  
    }  
}  
​  
// Concrete Observer: Logistics System  
public class LogisticsSystem implements OrderObserver {  
    @Override  
    public void update(Order order) {  
        if ("PAID".equals(order.getStatus())) {  
            System.out.println("Logistics System Notified: Order #" + order.getId() + " paid. Preparing shipment.");  
            // Logic to arrange shipment  
        } else if ("CANCELLED".equals(order.getStatus())) {  
            System.out.println("Logistics System Notified: Order #" + order.getId() + " cancelled. Canceling delivery.");  
            // Logic to cancel delivery  
        }  
    }  
}  
​  
// Concrete Observer: SMS Notification System  
public class SMSNotificationSystem implements OrderObserver {  
    @Override  
    public void update(Order order) {  
        System.out.println("SMS System Notified: Sending status update SMS to user " + order.getUserPhone());  
        // Logic to send SMS  
    }  
}  
​  
// Subject: Order Class  
public class Order {  
    private String id;  
    private String status;  
    private String userPhone;  
    private List<OrderObserver> observers = new ArrayList<>();  
      
    public Order(String id, String userPhone) {  
        this.id = id;  
        this.userPhone = userPhone;  
        this.status = "CREATED";  
    }  
      
    // Add observer  
    public void addObserver(OrderObserver observer) {  
        observers.add(observer);  
    }  
      
    // Remove observer  
    public void removeObserver(OrderObserver observer) {  
        observers.remove(observer);  
    }  
      
    // Notify all observers  
    private void notifyObservers() {  
        for (OrderObserver observer : observers) {  
            observer.update(this);  
        }  
    }  
      
    // Update order status  
    public void setStatus(String status) {  
        this.status = status;  
        System.out.println("Order #" + id + " status changed to: " + status);  
        notifyObservers();  
    }  
      
    // getter methods  
    public String getId() { return id; }  
    public String getStatus() { return status; }  
    public String getUserPhone() { return userPhone; }  
}
```

Usage:

```java
// Create Order  
Order order = new Order("12345", "13800138000");  
​  
// Add Observers  
order.addObserver(new InventorySystem());  
order.addObserver(new LogisticsSystem());  
order.addObserver(new SMSNotificationSystem());  
​  
// After payment, status changes  
order.setStatus("PAID");  
​  
// Output:  
// Order #12345 status changed to: PAID  
// Inventory System Notified: Order #12345 paid. Deducting stock.  
// Logistics System Notified: Order #12345 paid. Preparing shipment.  
// SMS System Notified: Sending status update SMS to user 13800138000
```

The benefit here is that if we need to add a new process when the order status changes (like adding a Points System), we only need to create a new observer and register it with the order object, without modifying the Order class code.

## 5. Decorator Pattern: Dynamic Function Extension

### What is the Decorator Pattern?

The Decorator Pattern allows you to add new functionality to an existing object without altering its structure.

### When to use it?

- ==When you need to dynamically add features to an object, and these features can be combined.==
- When you don’t want to use inheritance to extend functionality.
- When you need to choose different functional combinations at runtime.

### Real-world Example

A coffee ordering system is a perfect example. A basic coffee can have various add-ons (Milk, Sugar, Chocolate, etc.), and each add-on affects the final price.

```java
// Coffee Interface  
public interface Coffee {  
    String getDescription();  
    double getCost();  
}  
​  
// Basic Coffee Class  
public class SimpleCoffee implements Coffee {  
    @Override  
    public String getDescription() {  
        return "Simple Coffee";  
    }  
      
    @Override  
    public double getCost() {  
        return 10.0; // Base price  
    }  
}  
​  
// Decorator Base Class  
public abstract class CoffeeDecorator implements Coffee {  
    protected Coffee decoratedCoffee;  
      
    public CoffeeDecorator(Coffee coffee) {  
        this.decoratedCoffee = coffee;  
    }  
      
    @Override  
    public String getDescription() {  
        return decoratedCoffee.getDescription();  
    }  
      
    @Override  
    public double getCost() {  
        return decoratedCoffee.getCost();  
    }  
}  
​  
// Concrete Decorator: Milk  
public class MilkDecorator extends CoffeeDecorator {  
    public MilkDecorator(Coffee coffee) {  
        super(coffee);  
    }  
      
    @Override  
    public String getDescription() {  
        return decoratedCoffee.getDescription() + " + Milk";  
    }  
      
    @Override  
    public double getCost() {  
        return decoratedCoffee.getCost() + 2.0; // Milk adds $2  
    }  
}  
​  
// Concrete Decorator: Sugar  
public class SugarDecorator extends CoffeeDecorator {  
    public SugarDecorator(Coffee coffee) {  
        super(coffee);  
    }  
      
    @Override  
    public String getDescription() {  
        return decoratedCoffee.getDescription() + " + Sugar";  
    }  
      
    @Override  
    public double getCost() {  
        return decoratedCoffee.getCost() + 1.0; // Sugar adds $1  
    }  
}  
​  
// Concrete Decorator: Chocolate  
public class ChocolateDecorator extends CoffeeDecorator {  
    public ChocolateDecorator(Coffee coffee) {  
        super(coffee);  
    }  
      
    @Override  
    public String getDescription() {  
        return decoratedCoffee.getDescription() + " + Chocolate";  
    }  
      
    @Override  
    public double getCost() {  
        return decoratedCoffee.getCost() + 3.0; // Chocolate adds $3  
    }  
}
```

Usage:

```java
// Create a simple coffee  
Coffee coffee = new SimpleCoffee();  
System.out.println(coffee.getDescription() + " Price: " + coffee.getCost());  
​  
// Add Milk  
coffee = new MilkDecorator(coffee);  
System.out.println(coffee.getDescription() + " Price: " + coffee.getCost());  
​  
// Add Sugar  
coffee = new SugarDecorator(coffee);  
System.out.println(coffee.getDescription() + " Price: " + coffee.getCost());  
​  
// Add Chocolate  
coffee = new ChocolateDecorator(coffee);  
System.out.println(coffee.getDescription() + " Price: " + coffee.getCost());  
​  
// Output:  
// Simple Coffee Price: 10.0  
// Simple Coffee + Milk Price: 12.0  
// Simple Coffee + Milk + Sugar Price: 13.0  
// Simple Coffee + Milk + Sugar + Chocolate Price: 16.0
```

The advantage of the Decorator Pattern is that you can dynamically combine various toppings without creating a subclass for every combination.

Imagine if we used inheritance; we would need to create classes like “CoffeeWithMilk”, “CoffeeWithSugar”, “CoffeeWithMilkAndSugar”, etc.

The more combinations, the more classes we would have.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*rXTNI6xfCpIfH4DC)

Photo by [Matt Hardy](https://unsplash.com/@matthardy?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 6. Adapter Pattern: Compatible Interfaces

### What is the Adapter Pattern?

The Adapter Pattern allows classes with incompatible interfaces to work together.

### When to use it?

- You need to use an existing class, but its interface doesn’t match your requirements.
- You need to unify the interfaces of multiple classes.
- You need to reuse existing classes, but their interfaces don’t fit the system’s needs.

### Real-world Example

Suppose our system needs to integrate multiple third-party payment platforms, but every platform has a different API interface.

We can use the Adapter Pattern to unify these interfaces.

```java
// The unified payment interface defined in our system  
public interface PaymentProcessor {  
    boolean processPayment(String orderId, double amount, String currency);  
    PaymentStatus checkStatus(String paymentId);  
}  
​  
// Original Interface of 3rd Party Platform A (e.g., Alipay SDK)  
public class AlipaySDK {  
    public String pay(String orderNo, double money, String type) {  
        System.out.println("Processing Alipay: " + orderNo + ", Amount: " + money);  
        // Actually call Alipay API  
        return "ALI" + System.currentTimeMillis(); // Return Alipay Trade No  
    }  
      
    public int queryPayStatus(String tradeNo) {  
        System.out.println("Querying Alipay Status: " + tradeNo);  
        // Actually query Alipay status  
        return 1; // 1: Success, 0: Processing, -1: Failed  
    }  
}  
​  
// Original Interface of 3rd Party Platform B (e.g., WeChat Pay API)  
public class WeChatPayAPI {  
    public Map<String, String> createPayment(Map<String, Object> payData) {  
        String orderId = (String) payData.get("out_trade_no");  
        Double amount = (Double) payData.get("total_fee");  
        System.out.println("Processing WeChat Pay: " + orderId + ", Amount: " + amount);  
        // Actually call WeChat API  
        Map<String, String> result = new HashMap<>();  
        result.put("transaction_id", "WX" + System.currentTimeMillis());  
        result.put("result_code", "SUCCESS");  
        return result;  
    }  
      
    public Map<String, String> queryPaymentStatus(String transactionId) {  
        System.out.println("Querying WeChat Status: " + transactionId);  
        // Actually query WeChat status  
        Map<String, String> result = new HashMap<>();  
        result.put("trade_state", "SUCCESS"); // SUCCESS, PROCESSING, FAILED  
        return result;  
    }  
}  
​  
// Payment Status Enum  
public enum PaymentStatus {  
    SUCCESS, PROCESSING, FAILED  
}  
​  
// Alipay Adapter  
public class AlipayAdapter implements PaymentProcessor {  
    private AlipaySDK alipaySDK;  
    private Map<String, String> orderToPaymentMap = new HashMap<>();  
      
    public AlipayAdapter() {  
        this.alipaySDK = new AlipaySDK();  
    }  
      
    @Override  
    public boolean processPayment(String orderId, double amount, String currency) {  
        try {  
            String paymentId = alipaySDK.pay(orderId, amount, "direct");  
            orderToPaymentMap.put(orderId, paymentId);  
            return true;  
        } catch (Exception e) {  
            return false;  
        }  
    }  
      
    @Override  
    public PaymentStatus checkStatus(String orderId) {  
        String paymentId = orderToPaymentMap.get(orderId);  
        if (paymentId == null) {  
            return PaymentStatus.FAILED;  
        }  
          
        int status = alipaySDK.queryPayStatus(paymentId);  
        switch (status) {  
            case 1: return PaymentStatus.SUCCESS;  
            case 0: return PaymentStatus.PROCESSING;  
            default: return PaymentStatus.FAILED;  
        }  
    }  
}  
​  
// WeChat Pay Adapter  
public class WeChatPayAdapter implements PaymentProcessor {  
    private WeChatPayAPI weChatPayAPI;  
    private Map<String, String> orderToPaymentMap = new HashMap<>();  
      
    public WeChatPayAdapter() {  
        this.weChatPayAPI = new WeChatPayAPI();  
    }  
      
    @Override  
    public boolean processPayment(String orderId, double amount, String currency) {  
        try {  
            Map<String, Object> payData = new HashMap<>();  
            payData.put("out_trade_no", orderId);  
            payData.put("total_fee", amount);  
            payData.put("fee_type", currency);  
              
            Map<String, String> result = weChatPayAPI.createPayment(payData);  
            if ("SUCCESS".equals(result.get("result_code"))) {  
                orderToPaymentMap.put(orderId, result.get("transaction_id"));  
                return true;  
            }  
            return false;  
        } catch (Exception e) {  
            return false;  
        }  
    }  
      
    @Override  
    public PaymentStatus checkStatus(String orderId) {  
        String transactionId = orderToPaymentMap.get(orderId);  
        if (transactionId == null) {  
            return PaymentStatus.FAILED;  
        }  
          
        Map<String, String> result = weChatPayAPI.queryPaymentStatus(transactionId);  
        String status = result.get("trade_state");  
        switch (status) {  
            case "SUCCESS": return PaymentStatus.SUCCESS;  
            case "PROCESSING": return PaymentStatus.PROCESSING;  
            default: return PaymentStatus.FAILED;  
        }  
    }  
}
```

Usage:

```java
// Order Processing Logic  
public class OrderService {  
    private PaymentProcessor paymentProcessor;  
      
    public OrderService(PaymentProcessor paymentProcessor) {  
        this.paymentProcessor = paymentProcessor;  
    }  
      
    public boolean processOrder(String orderId, double amount, String currency) {  
        // Order processing logic...  
          
        // Call the unified payment interface  
        boolean paymentSuccess = paymentProcessor.processPayment(orderId, amount, currency);  
          
        if (paymentSuccess) {  
            System.out.println("Order payment processing: " + orderId);  
            // Other order logic...  
            return true;  
        } else {  
            System.out.println("Order payment failed: " + orderId);  
            return false;  
        }  
    }  
      
    public void checkOrderPaymentStatus(String orderId) {  
        PaymentStatus status = paymentProcessor.checkStatus(orderId);  
        System.out.println("Payment status for Order " + orderId + ": " + status);  
          
        // Handle order based on payment status...  
    }  
}  
​  
// Client Code  
public static void main(String[] args) {  
    // Decide which payment method to use based on config or user selection  
    String paymentMethod = "alipay"; // or "wechat"  
      
    PaymentProcessor paymentProcessor;  
    if ("alipay".equals(paymentMethod)) {  
        paymentProcessor = new AlipayAdapter();  
    } else {  
        paymentProcessor = new WeChatPayAdapter();  
    }  
      
    OrderService orderService = new OrderService(paymentProcessor);  
      
    // Process Order  
    String orderId = "ORD" + System.currentTimeMillis();  
    orderService.processOrder(orderId, 99.9, "CNY");  
      
    // Check payment status later  
    orderService.checkOrderPaymentStatus(orderId);  
}
```

Through the Adapter Pattern, we can adapt different payment platform APIs to the unified interface defined by our system.

This way, the order processing logic doesn’t need to care about which specific payment platform is being used, greatly improving the flexibility and maintainability of the system.

## Summary

In actual development, don’t deliberately force a specific pattern. Instead, choose the appropriate solution based on the actual problem.

When you find your code structure looks very similar to a certain design pattern, it means you have unconsciously started using design patterns.

The patterns introduced above are the most commonly used in Java development.

Mastering them is very helpful for improving code quality and maintainability.

Of course, there are many other design patterns, such as the Builder Pattern, Proxy Pattern, Template Method Pattern, etc.

If you are interested, feel free to leave a comment to dive deeper.

Hope this article helps you. Happy coding!

## A message from our Founder

**Hey,** [**Sunil**](https://linkedin.com/in/sunilsandhu) **here.** I wanted to take a moment to thank you for reading until the end and for being a part of this community.

Did you know that our team run these publications as a volunteer effort to over 3.5m monthly readers? **We don’t receive any funding, we do this to support the community. ❤️**

If you want to show some love, please take a moment to **follow me on** [**LinkedIn**](https://linkedin.com/in/sunilsandhu)**,** [**TikTok**](https://tiktok.com/@messyfounder), [**Instagram**](https://instagram.com/sunilsandhu). You can also subscribe to our [**weekly newsletter**](https://newsletter.plainenglish.io/).

And before you go, don’t forget to **clap** and **follow** the writer️!