---
title: "Rust:axum学习笔记(1) hello world"
date: 2024-01-18 10:19:41
---
# Rust:axum学习笔记(1) hello world

[axum](https://github.com/tokio-rs/axum)是Rust生态的web框架新秀，虽然项目成立不久，但github上的star数已超2.8k,其底层依赖的是高性能的[Tokio](https://github.com/tokio-rs/tokio)，Tokio这货就不多说了，借用[知乎《深入浅出Rust异步编程之Tokio》](https://zhuanlan.zhihu.com/p/107820568?from_voters_page=true)上的一张图：

![img](https://img2020.cnblogs.com/blog/27612/202112/27612-20211226204451793-2114282534.png)

Rust中的Tokio几乎是同类框架的性能天花板了，而axum在Tokio基础上构建，起点就站在巨人的肩膀上。

## 一.helloword

先来一个helloword的案例：

cargo:

```plaintext
[dependencies]
tokio = {version = '1',features=["full"]}
axum = "0.7.4"
```

```rust
use axum::{
    routing::get,
    Router,
};
// 这里使用了use关键字导入了Axum库中的get函数和Router结构体。get函数用于定义HTTP GET请求的路由处理，Router结构体用于创建路由器。

// 这是一个异步的入口函数，使用了#[tokio::main]属性宏来告诉编译器使用Tokio运行时来启动异步任务。
#[tokio::main]
async fn main(){
// 这行代码创建了一个Router实例，并使用route方法将根路径 / 和一个处理函数绑定起来。这个处理函数使用了闭包语法，以异步的方式返回了一个字符串"hello word"作为响应。
    let app = Router::new().route("/",get(|| async {"hello word"}));
    
    let listener = tokio::net::TcpListener::bind("127.0.0.1:9000").await.unwrap();

    axum::serve(listener,app).await.unwrap();     
}
```

## 二.路由

路由设置路径可以使用handler，类似于springboot的controlle里的方法

```rust
use axum::{
    routing::get,
    Router,
};

#[tokio::main]
async fn main(){
    let app = Router::new()
    .route("/",get(root)) // 路径对应handler
    .route("/foo",get(get_foo).post(post_foo))
    .route("/foo/bar",get(foo_bar));
    
    let listener = tokio::net::TcpListener::bind("127.0.0.1:9000").await.unwrap();

    axum::serve(listener,app).await.unwrap();
    
    async fn root(){
        println!("root");
    }
    async fn get_foo(){
        println!("get_foo");
    }

    async fn post_foo(){
        println!("post_foo");
    }

    async fn foo_bar(){
        println!("foo_bar");
    }
}
```

nest方法可以嵌套一些别的路由

```rust
use axum::{
    routing::{get,post},
    Router,
};

#[tokio::main]
async fn main(){

    let user_routes = Router::new().route("/:id",get (|| async{}));
    let team_router = Router::new().route("/",post(|| async{}));
    let api_routes = Router::new()
        .nest("/users",user_routes)
        .nest("/teams", team_router);
    let app = Router::new().nest("/api", api_routes);
    
    let listener = tokio::net::TcpListener::bind("127.0.0.1:9000").await.unwrap();

    axum::serve(listener,app).await.unwrap();
    
}

//此时有两个路径
// - GET /api/users/:id
// - POST /api/teams
```

merge方法将两个路由器合并为一个

```plaintext
use axum::{
    routing::{get},
    Router,
};

#[tokio::main]
async fn main(){

    let user_routes = Router::new()
    .route("/users", get(|| async {}))
    .route("/users/:id", get(|| async {}));
// team路由
let team_routes = Router::new()
    .route("/teams", get(|| async {}));

// 合并
let app = Router::new()
    .merge(user_routes)
    .merge(team_routes);
    
    let listener = tokio::net::TcpListener::bind("127.0.0.1:9000").await.unwrap();

    axum::serve(listener,app).await.unwrap();
    
}
```

router可以接受多个handler方法，对于不同的请求方式

```plaintext
use axum::{
    routing::{get, delete},
    Router,
};

#[tokio::main]
async fn main(){
    let app = Router::new().route(
        "/",
        get(get_root).post(post_root).delete(delete_root),
    );
    async fn get_root() {}
    async fn post_root() {}
    async fn delete_root() {}
    
    let listener = tokio::net::TcpListener::bind("127.0.0.1:9000").await.unwrap();

    axum::serve(listener,app).await.unwrap();
    
}
```

## 三.handler和提取器

handler是一个异步函数，它接受零个或多个“提取器”作为参数并返回一些 可以转换为响应。  
处理程序是应用程序逻辑所在的位置，也是构建 axum 应用程序的位置 通过在处理程序之间路由。  
它采用任意数量的 “提取器”作为参数。提取器是实现 FromRequest 或 FromRequestPart 的类型

例如，Json 提取器，它使用请求正文和 将其反序列化为 JSON 为某种目标类型，可以用来解析json格式

```plaintext
use axum::{
    routing::post,
    handler::Handler,
    extract::Json,
    Router,
};
use serde::Deserialize;
#[derive(Deserialize)]
struct CreateUser{
    email:String,
    password:String,
}

async fn create_user(Json(payload):Json<CreateUser>){
    // 这里payload参数类型为CreateUser结构体，并且字段参数已经被赋值
}
#[tokio::main]
async fn main(){
    
    let app = Router::new().route("/users", post(create_user));
    
    let listener = tokio::net::TcpListener::bind("127.0.0.1:9000").await.unwrap();

    axum::serve(listener,app).await.unwrap();
    
}
```

注意需要引入serde 依赖

```plaintext
serde = {version = "1.0.195" ,features = ["derive"]}
serde_json = "1.0.111"
```

还有一些其他的常用的提取器，用于解析不同类型参数

```plaintext
use axum::{
    extract::{Json, TypedHeader, Path, Extension, Query},
    routing::post,
    headers::UserAgent,
    http::{Request, header::HeaderMap},
    body::{Bytes, Body},
    Router,
};
use serde_json::Value;
use std::collections::HashMap;

// `Path`用于解析路径上的参数，比如/path/:user_id，这时候请求路径/path/100，那么user_id的值就是100，类似springboot当中@PathVariable注解
async fn path(Path(user_id): Path<u32>) {}

// 查询路径请求参数值，这里转换成hashmap对象了，类似springboot当中@RequestParam注解
async fn query(Query(params): Query<HashMap<String, String>>) {}

// `HeaderMap`可以获取所有请求头的值
async fn headers(headers: HeaderMap) {}

//TypedHeader可以用于提取单个标头（header），请注意这需要您启用了axum的headers功能
async fn user_agent(TypedHeader(user_agent): TypedHeader<UserAgent>) {}

//获得请求体中的数据，按utf-8编码
async fn string(body: String) {}

//获得请求体中的数据，字节类型
async fn bytes(body: Bytes) {}

//这个使json类型转换成结构体，上面的例子讲了
async fn json(Json(payload): Json<Value>) {}

// 这里可以获取Request，可以自己去实现更多功能
async fn request(request: Request<Body>) {}

//Extension从"请求扩展"中提取数据。这里可以获得共享状态
async fn extension(Extension(state): Extension<State>) {}

//程序的共享状态，需要实现Clone
#[derive(Clone)]
struct State { /* ... */ }

let app = Router::new()
    .route("/path/:user_id", post(path))
    .route("/query", post(query))
    .route("/user_agent", post(user_agent))
    .route("/headers", post(headers))
    .route("/string", post(string))
    .route("/bytes", post(bytes))
    .route("/json", post(json))
    .route("/request", post(request))
    .route("/extension", post(extension));
```

每个handler参数可以使用多个提取器提取参数

```plaintext
use axum::{
    extract::{Path, Query},
    routing::get,
    Router,
};
use uuid::Uuid;
use serde::Deserialize;

let app = Router::new().route("/users/:id/things", get(get_user_things));

#[derive(Deserialize)]
struct Pagination {
    page: usize,
    per_page: usize,
}

impl Default for Pagination {
    fn default() -> Self {
        Self { page: 1, per_page: 30 }
    }
}

async fn get_user_things(
    Path(user_id): Path<Uuid>,
    pagination: Option<Query<Pagination>>,
) {
    let Query(pagination) = pagination.unwrap_or_default();

    // ...
}
```
