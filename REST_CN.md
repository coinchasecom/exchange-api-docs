# 概要
    域名: https://api.coinchase.com
    API Key: https://coinchase.com/me/user_api
    
    需要签名的接口如下:
    /x/v1/order/buy
    /x/v1/order/sell
    /x/v1/order/cancelOne
    /x/v1/order/cancelByPair
    /x/v1/order/pending
    /x/v1/order/history
# 签名
    签名算法使用HmacSHA256
    
    以买入接口举例/x/v1/order/buy
    
    签名函数:
    function Sign(mapParams, method, hostname, path, secretKey string) signature
    
    Sign函数传参
    mapParams:      类型:Map, 不管是GET还是POST，都需要把所有参数保存到map。如?pair=ABC/USDT
                    则mapParams[pair] = ABC/USDT
                    
    method:         类型:String, 请求方式GET则method="GET"
    
    hostname:       类型:String, hostname="api.coinchase.com"
    
    path:           类型:String, path="/x/v1/order/buy"
    
    secretKey:      类型:String, secretKey="your secret key"
    
    Sign函数返回值
    signature:      类型:String, 用secretKey签名所得到的值
    
    签名步骤:
    0.  为mapParams添加timestamp字段，为当前时间戳(10位)
    
    1、 先把mapParams中的item的key按照ASCII码排序后生成一个新的排序后的map。比如下面是原始的参数：
        price=1.2
        volume=2.9
        pair=ABC/USDT
        timestamp=1527382810
        
        排序之后应该为：
        pair=ABC/USDT
        price=1.2
        timestamp=1527382810
        volume=2.9
    
    2.  把排好序的mapParams转换成字符串strParams 类似url query 如下:
        "pair=ABC/USDT&price=1.2&timestamp=1527382810&volume=2.9"
    
    3.  拼装待签名内容data
        data = method + "\n" + hostname + "\n" + path + "\n" + strParams
    
    4.  计算签名值
        用HmacSHA256算法，以secretKey为钥匙给data签名，得到一个返回值value，
        然后使用base64算法的standard base64 encoding，把value转换成base64格式的字符串signature
        signature就是得到的签名值
    
    发起请求:
    1.  最后把得到的签名值添加到请求参数中，以字段signature
    
    注意事项:
    1.  每个需要签名的接口都需要包含timestamp和signature字段
    2.  在签名前就需要把timestamp字段设置好
    
# APIs
### Order
- 申报买单
    ```
    path: /x/v1/order/buy
    签名: 需要
    method: POST
    request body:
    {
        "pair": "ABC/USDT", // 交易对 String
        "price": "3.121", // 价格 String （计价货币USDT）
        "volume": "2.21", // 数量 String （基础货币ABC)
    }
    response: 
    {
        "code": 200,
        "err": "",
        "data": 
        {
            id: "1206980768090296320" // 订单ID
            pair: "ABC/USDT"
            email: "a@b.com" // 我的账号
            price: "0.000059" // 申报价格 USDT
            volume: "10000" // 申报数量 ABC
            quote_amount: "0.59" // 总申报金额 USDT
            deal_volume: "0" // 成交数量 ABC
            remain_volume: "10000" // 剩余未成交数量 ABC
            deal_amount: "0" // 成交总金额 USDT
            deal_fee: "0" // 交易费 买则以ABC计，卖则以USDT计
            created_at: 1576601614388040 // 创建时间戳 16位
            used_up_at: 0 // 完全成交完毕时间戳 16位
            is_canceled: false // 是否撤单
            canceled_at: 0 // 撤单时间戳 16位
            updated_at: 0 // 更新时间戳 16位 可忽略此字段
            kind: 1 // 1是买单，2是卖单
        }
    }
    ```
- 申报卖单
    ```
    path: /x/v1/order/sell
    签名: 需要
    method: POST
    request body:
    {
        "pair": "ABC/USDT", // 交易对 String
        "price": "3.121", // 价格 String （计价货币USDT）
        "volume": "2.21", // 数量 String （基础货币ABC)
    }
    response: 
    {
        "code": 200,
        "err": "",
        "data": 
        {
            id: "1206980768090296320" // 订单ID
            pair: "ABC/USDT"
            email: "a@b.com" // 我的账号
            price: "0.000059" // 申报价格 USDT
            volume: "10000" // 申报数量 ABC
            quote_amount: "0.59" // 总申报金额 USDT
            deal_volume: "0" // 成交数量 ABC
            remain_volume: "10000" // 剩余未成交数量 ABC
            deal_amount: "0" // 成交总金额 USDT
            deal_fee: "0" // 交易费 买则以ABC计，卖则以USDT计
            created_at: 1576601614388040 // 创建时间戳 16位
            used_up_at: 0 // 完全成交完毕时间戳 16位
            is_canceled: false // 是否撤单
            canceled_at: 0 // 撤单时间戳 16位
            updated_at: 0 // 更新时间戳 16位 可忽略此字段
            kind: 2 // 1是买单，2是卖单
        }
    }
    ```
- 撤销指定订单
    ```
    path: /x/v1/order/cancelOne
    签名: 需要
    method: POST
    request body:
    {
        "id": "1206980768090296320", // String 订单id
        "kind": "1" // String 订单类型1是买单，2是卖单
    }
    response: 
    {
        "code": 200,
        "err": "",
        "data": ""
    }
    ```
- 撤销指定交易对的所有订单
    ```
    path: /x/v1/order/cancelByPair
    签名: 需要
    method: POST
    url params: pair=ABC/USDT
    request body: 无
    
    例子: https://api.coinchase.com/x/v1/order/cancelByPair?pair=ABC/USDT
    
    response: 
    {
        "code": 200,
        "err": "",
        "data": ""
    }
    
    注意: 签名的字段有pair和timestamp
    ```

- 查询我的未成交完毕的订单
    ```
    path: /x/v1/order/pending
    签名: 需要
    method: GET
    url params:
    {
        pair	// 非必填, 如ABC/USDT（不填则作用域为所有交易对）
        side	// 非必填, 指定只返回某一个方向的订单，可能的值有: 1,2 1是买2是卖，不填则两个方向都返回。
        from	// 非必填, 查询起始 ID
        direct	// 如字段'from'已设定，此字段'direct'为必填, 查询方向 (asc - 以起始ID升序检索；desc - 以起始ID降序检索)
        size	// 非必填, 返回订单的数量，最大值500默认值100。
    }
    request body: 无
    response: 
    {
        "code": 200,
        "err": "",
        "data":
        [
            {
                id: "1206980768090296320" // 订单ID
                pair: "ABC/USDT"
                email: "a@b.com" // 我的账号
                price: "0.000059" // 申报价格 USDT
                volume: "10000" // 申报数量 ABC
                quote_amount: "0.59" // 总申报金额 USDT
                deal_volume: "0" // 成交数量 ABC
                remain_volume: "10000" // 剩余未成交数量 ABC
                deal_amount: "0" // 成交总金额 USDT
                deal_fee: "0" // 交易费 买则以ABC计，卖则以USDT计
                created_at: 1576601614388040 // 创建时间戳 16位
                used_up_at: 0 // 完全成交完毕时间戳 16位
                is_canceled: false // 是否撤单
                canceled_at: 0 // 撤单时间戳 16位
                updated_at: 0 // 更新时间戳 16位 可忽略此字段
                kind: 2 // 1是买单，2是卖单
            },
            ...
        ]
    }
    ```

- 查询我的历史订单（包括已撤销和已成交完毕）
    ```
    path: /x/v1/order/history
    签名: 需要
    method: GET
    url params: 
    {
        pair	// 必填, 如ABC/USDT
        side	// 非必填, 指定只返回某一个方向的订单，可能的值有: 1,2 1是买2是卖，不填则两个方向都返回。
        startTs // 非必填, 起始查询时间戳 10位, 默认值一周前
        endTs   // 非必填, 截止查询时间戳 10位, 默认值现在
        from	// 非必填, 查询起始 ID
        direct	// 如字段'from'已设定，此字段'direct'为必填, 查询方向 (asc - 以起始ID升序检索；desc - 以起始ID降序检索)
        size	// 非必填, 返回订单的数量，最大值500默认值100。
    }
    request body: 无
    response: 
    {
        "code": 200,
        "err": "",
        "data":
        [
            {
                id: "1206980768090296320" // 订单ID
                pair: "ABC/USDT"
                email: "a@b.com" // 我的账号
                price: "0.000059" // 申报价格 USDT
                volume: "10000" // 申报数量 ABC
                quote_amount: "0.59" // 总申报金额 USDT
                deal_volume: "0" // 成交数量 ABC
                remain_volume: "10000" // 剩余未成交数量 ABC
                deal_amount: "0" // 成交总金额 USDT
                deal_fee: "0" // 交易费 买则以ABC计，卖则以USDT计
                created_at: 1576601614388040 // 创建时间戳 16位
                used_up_at: 0 // 完全成交完毕时间戳 16位
                is_canceled: false // 是否撤单
                canceled_at: 0 // 撤单时间戳 16位
                updated_at: 0 // 更新时间戳 16位 可忽略此字段
                kind: 2 // 1是买单，2是卖单
            },
            ...
        ]
    }
    ```