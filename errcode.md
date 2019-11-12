# 响应码
!>系统定义了四类响应码

此外，因某些异常导致框架崩溃或未正常给出响应，会直接报HTTP错误，不做举例。

# 响应码定义

| 类型 | 定义 | 说明 | 
|---|---|---|
| 成功 | success：“0” | 业务处理成功 |
| 中间件级别错误（由中间件捕获） | middule_headers_incomplete:'10' | 请求头业务参数不全 |
|  | middule_headers_err:'11' | 请求头业务参数值不合法 |
|  | middule_headers_unique:'12' | 请求头业务参数唯一性错误 |
| 全局级别错误（由框架捕获） | global_router_nun:'20' | API或静态资源不存在 |
|  | global_router_auth:'21' | API权限不足 |
|  | global_datebase:'22' | 数据库连接或处理错误 |
|  | global_other:'23' | 其他没预期到的错误 |
| API级别错误（由API捕获） | api_body_incomplete:'30' | 业务参数不全 |
|  | api_body_type:'31' | 业务参数类型错误 |
|  | api_body_err:'32' | 业务参数值错误 |
|  | api_body_unique:'33' | 业务参数唯一性错误 |
|  | api_other:'34' | 其他没预期到的api级别错误 |


