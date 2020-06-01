# Tqsdk 接入新版合约服务开发设计

相关文档：
+ [合约服务文档](https://shinnytech.atlassian.net/wiki/spaces/EFS/pages/29786314/GraphQL+Api)


每个模块可以正确处理 `aid = "ins_query"` 包。

## TqAccount

不需要合约信息，只需要正确转发 `aid = "ins_query"` 的请求包。

## TqSim

需要合约信息

1. 需要正确转发 `aid = "ins_query"` 的请求包。
2. 在合约撮合成交 task 的第一步，先请求合约服务的信息，等到收到必要的字段再执行后续流程。

## TqBacktest

需要合约信息

1. 需要正确转发 `aid = "ins_query"` 的请求包。
2. 为每个合约在初始化 generator 的时候，请求一次合约信息，等到收到必要的字段再执行后续流程。

## TqApi

不需要合约信息

1. 需要能够处理 `aid = "ins_query"` 的请求包和返回的 `symbols` 数据。
2. 提供类似现在 get_quote 的同步获取合约信息的函数，get_symbol, 在同步代码中等到收到合约信息才返回，在异步代码中只发送 query。
3. 提供异步函数 `async def graph_query`，用户可以使用 `result = await graph_query("xxxxxxxxx")` 获取合约服务。
4. 2，3 两个函数先检查是否已有相同的 query，有的话直接返回结果。
5. 在用户使用 `api._data` 的时候，显示错误，并提供给用户推荐用法。
6. 新增加几个接口提供给用户使用，能够完成 https://shinnytech.atlassian.net/browse/BE-247 https://shinnytech.atlassian.net/browse/BE-248
 
```
# 全部 期货 期权 指数 主力连续 组合
def query_quotes(ins_class: str = None, exchange_id: str = None, expired: bool = None, has_night: bool = None, has_derivatives: bool = None, product_id: str = None):
# 主连对应的标的合约
def query_cont_quotes(exchange_id: str = None, product_id: str = None):
# 查询符合条件的期权
def query_options(underlying_symbol:str=None, option_class=None, option_month=None, strike_price=None, has_A=None):
```

## utils

1. 需要函数生成 query 模板
