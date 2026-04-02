## 代数数据类型与 C# 联合体

> **本章要点：** Rust 的代数数据类型（带数据的枚举）与 C# 有限的可辨识联合体对比、
> 带穷举检查的 `match` 表达式、守卫子句，以及嵌套模式解构。
>
> **难度：** 🟡 中级

### C# 可辨识联合体（有限支持）
```csharp
// C# - 通过继承实现有限的联合体支持
public abstract class Result
{
    public abstract T Match<T>(Func<Success, T> onSuccess, Func<Error, T> onError);
}

public class Success : Result
{
    public string Value { get; }
    public Success(string value) => Value = value;
    
    public override T Match<T>(Func<Success, T> onSuccess, Func<Error, T> onError)
        => onSuccess(this);
}

public class Error : Result
{
    public string Message { get; }
    public Error(string message) => Message = message;
    
    public override T Match<T>(Func<Success, T> onSuccess, Func<Error, T> onError)
        => onError(this);
}

// C# 9+ Records 配合模式匹配（更好的方式）
public abstract record Shape;
public record Circle(double Radius) : Shape;
public record Rectangle(double Width, double Height) : Shape;

public static double Area(Shape shape) => shape switch
{
    Circle(var radius) => Math.PI * radius * radius,
    Rectangle(var width, var height) => width * height,
    _ => throw new ArgumentException("Unknown shape")  // [错误] 可能发生运行时错误
};
```

### Rust 代数数据类型（枚举）
```rust
// Rust - 具有穷举模式匹配的真正代数数据类型
#[derive(Debug, Clone)]
pub enum Result<T, E> {
    Ok(T),
    Err(E),
}

#[derive(Debug, Clone)]
pub enum Shape {
    Circle { radius: f64 },
    Rectangle { width: f64, height: f64 },
    Triangle { base: f64, height: f64 },
}

impl Shape {
    pub fn area(&self) -> f64 {
        match self {
            Shape::Circle { radius } => std::f64::consts::PI * radius * radius,
            Shape::Rectangle { width, height } => width * height,
            Shape::Triangle { base, height } => 0.5 * base * height,
            // [正确] 如果缺少任何变体，编译器会报错！
        }
    }
}

// 进阶：枚举可以持有不同类型
#[derive(Debug)]
pub enum Value {
    Integer(i64),
    Float(f64),
    Text(String),
    Boolean(bool),
    List(Vec<Value>),  // 递归类型！
}

impl Value {
    pub fn type_name(&self) -> &'static str {
        match self {
            Value::Integer(_) => "integer",
            Value::Float(_) => "float",
            Value::Text(_) => "text",
            Value::Boolean(_) => "boolean",
            Value::List(_) => "list",
        }
    }
}
```

```mermaid
graph TD
    subgraph "C# 可辨识联合体（变通方案）"
        CS_ABSTRACT["abstract class Result"]
        CS_SUCCESS["class Success : Result"]
        CS_ERROR["class Error : Result"]
        CS_MATCH["手动 Match 方法\n或 switch 表达式"]
        CS_RUNTIME["[错误] 缺少分支导致\n运行时异常"]
        CS_HEAP["[错误] 类继承导致\n堆分配"]
        
        CS_ABSTRACT --> CS_SUCCESS
        CS_ABSTRACT --> CS_ERROR
        CS_SUCCESS --> CS_MATCH
        CS_ERROR --> CS_MATCH
        CS_MATCH --> CS_RUNTIME
        CS_ABSTRACT --> CS_HEAP
    end
    
    subgraph "Rust 代数数据类型"
        RUST_ENUM["enum Shape { ... }"]
        RUST_VARIANTS["Circle { radius }<br/>Rectangle { width, height }<br/>Triangle { base, height }"]
        RUST_MATCH["match shape { ... }"]
        RUST_EXHAUSTIVE["[正确] 穷举检查\n编译时保证"]
        RUST_STACK["[正确] 栈分配\n高效内存使用"]
        RUST_ZERO["[正确] 零成本抽象"]
        
        RUST_ENUM --> RUST_VARIANTS
        RUST_VARIANTS --> RUST_MATCH
        RUST_MATCH --> RUST_EXHAUSTIVE
        RUST_ENUM --> RUST_STACK
        RUST_STACK --> RUST_ZERO
    end
    
    style CS_RUNTIME fill:#ffcdd2,color:#000
    style CS_HEAP fill:#fff3e0,color:#000
    style RUST_EXHAUSTIVE fill:#c8e6c9,color:#000
    style RUST_STACK fill:#c8e6c9,color:#000
    style RUST_ZERO fill:#c8e6c9,color:#000
```

***

## 枚举与模式匹配

Rust 的枚举比 C# 的枚举强大得多——它们可以持有数据，是类型安全编程的基础。

### C# 枚举的局限性
```csharp
// C# 枚举 - 仅是具名常量
public enum Status
{
    Pending,
    Approved,
    Rejected
}

// 带基础值的 C# 枚举
public enum HttpStatusCode
{
    OK = 200,
    NotFound = 404,
    InternalServerError = 500
}

// 复杂数据需要单独的类
public abstract class Result
{
    public abstract bool IsSuccess { get; }
}

public class Success : Result
{
    public string Value { get; }
    public override bool IsSuccess => true;
    
    public Success(string value)
    {
        Value = value;
    }
}

public class Error : Result
{
    public string Message { get; }
    public override bool IsSuccess => false;
    
    public Error(string message)
    {
        Message = message;
    }
}
```

### Rust 枚举的强大功能
```rust
// 简单枚举（类似 C# 枚举）
#[derive(Debug, PartialEq)]
enum Status {
    Pending,
    Approved,
    Rejected,
}

// 带数据的枚举（这正是 Rust 的优势所在！）
#[derive(Debug)]
enum Result<T, E> {
    Ok(T),      // 成功变体，持有类型 T 的值
    Err(E),     // 错误变体，持有类型 E 的错误
}

// 持有不同数据类型的复杂枚举
#[derive(Debug)]
enum Message {
    Quit,                       // 无数据
    Move { x: i32, y: i32 },   // 结构体式变体
    Write(String),             // 元组式变体
    ChangeColor(i32, i32, i32), // 多个值
}

// 实际案例：HTTP 响应
#[derive(Debug)]
enum HttpResponse {
    Ok { body: String, headers: Vec<String> },
    NotFound { path: String },
    InternalError { message: String, code: u16 },
    Redirect { location: String },
}
```

### 使用 Match 进行模式匹配
```csharp
// C# switch 语句（有限）
public string HandleStatus(Status status)
{
    switch (status)
    {
        case Status.Pending:
            return "Waiting for approval";
        case Status.Approved:
            return "Request approved";
        case Status.Rejected:
            return "Request rejected";
        default:
            return "Unknown status"; // 始终需要 default
    }
}

// C# 模式匹配（C# 8+）
public string HandleResult(Result result)
{
    return result switch
    {
        Success success => $"Success: {success.Value}",
        Error error => $"Error: {error.Message}",
        _ => "Unknown result" // 仍需兜底分支
    };
}
```

```rust
// Rust match - 穷举且强大
fn handle_status(status: Status) -> String {
    match status {
        Status::Pending => "Waiting for approval".to_string(),
        Status::Approved => "Request approved".to_string(),
        Status::Rejected => "Request rejected".to_string(),
        // 无需 default — 编译器确保穷举性
    }
}

// 带数据提取的模式匹配
fn handle_result<T, E>(result: Result<T, E>) -> String 
where 
    T: std::fmt::Debug,
    E: std::fmt::Debug,
{
    match result {
        Result::Ok(value) => format!("Success: {:?}", value),
        Result::Err(error) => format!("Error: {:?}", error),
        // 穷举 — 无需 default
    }
}

// 复杂模式匹配
fn handle_message(msg: Message) -> String {
    match msg {
        Message::Quit => "Goodbye!".to_string(),
        Message::Move { x, y } => format!("Move to ({}, {})", x, y),
        Message::Write(text) => format!("Write: {}", text),
        Message::ChangeColor(r, g, b) => format!("Change color to RGB({}, {}, {})", r, g, b),
    }
}

// HTTP 响应处理
fn handle_http_response(response: HttpResponse) -> String {
    match response {
        HttpResponse::Ok { body, headers } => {
            format!("Success! Body: {}, Headers: {:?}", body, headers)
        },
        HttpResponse::NotFound { path } => {
            format!("404: Path '{}' not found", path)
        },
        HttpResponse::InternalError { message, code } => {
            format!("Error {}: {}", code, message)
        },
        HttpResponse::Redirect { location } => {
            format!("Redirect to: {}", location)
        },
    }
}
```

### 守卫与高级模式
```rust
// 带守卫的模式匹配
fn describe_number(x: i32) -> String {
    match x {
        n if n < 0 => "negative".to_string(),
        0 => "zero".to_string(),
        n if n < 10 => "single digit".to_string(),
        n if n < 100 => "double digit".to_string(),
        _ => "large number".to_string(),
    }
}

// 匹配范围
fn describe_age(age: u32) -> String {
    match age {
        0..=12 => "child".to_string(),
        13..=19 => "teenager".to_string(),
        20..=64 => "adult".to_string(),
        65.. => "senior".to_string(),
    }
}

// 解构结构体和元组
```

<details>
<summary><strong>🏋️ 练习：命令解析器</strong>（点击展开）</summary>

**挑战**：使用 Rust 枚举为 CLI 命令系统建模。将字符串输入解析为 `Command` 枚举并执行每个变体。
对未知命令进行适当的错误处理。

```rust
// 起始代码 — 填写空白
#[derive(Debug)]
enum Command {
    // TODO: 添加 Quit、Echo(String)、Move { x: i32, y: i32 }、Count(u32) 等变体
}

fn parse_command(input: &str) -> Result<Command, String> {
    let parts: Vec<&str> = input.splitn(2, ' ').collect();
    // TODO: 匹配 parts[0] 并解析参数
    todo!()
}

fn execute(cmd: &Command) -> String {
    // TODO: 匹配每个变体并返回描述
    todo!()
}
```

<details>
<summary>🔑 解答</summary>

```rust
#[derive(Debug)]
enum Command {
    Quit,
    Echo(String),
    Move { x: i32, y: i32 },
    Count(u32),
}

fn parse_command(input: &str) -> Result<Command, String> {
    let parts: Vec<&str> = input.splitn(2, ' ').collect();
    match parts[0] {
        "quit" => Ok(Command::Quit),
        "echo" => {
            let msg = parts.get(1).unwrap_or(&"").to_string();
            Ok(Command::Echo(msg))
        }
        "move" => {
            let args = parts.get(1).ok_or("move requires 'x y'")?;
            let coords: Vec<&str> = args.split_whitespace().collect();
            let x = coords.get(0).ok_or("missing x")?.parse::<i32>().map_err(|e| e.to_string())?;
            let y = coords.get(1).ok_or("missing y")?.parse::<i32>().map_err(|e| e.to_string())?;
            Ok(Command::Move { x, y })
        }
        "count" => {
            let n = parts.get(1).ok_or("count requires a number")?
                .parse::<u32>().map_err(|e| e.to_string())?;
            Ok(Command::Count(n))
        }
        other => Err(format!("Unknown command: {other}")),
    }
}

fn execute(cmd: &Command) -> String {
    match cmd {
        Command::Quit           => "Goodbye!".to_string(),
        Command::Echo(msg)      => msg.clone(),
        Command::Move { x, y }  => format!("Moving to ({x}, {y})"),
        Command::Count(n)       => format!("Counted to {n}"),
    }
}
```

**关键要点**：
- 每个枚举变体可以持有不同的数据——无需类层次结构
- `match` 强制你处理每个变体，防止遗漏情况
- `?` 运算符可以整洁地链接错误传播——无需嵌套的 try-catch

</details>
</details>
