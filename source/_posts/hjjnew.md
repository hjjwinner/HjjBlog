title: IOS网络操作-使用Alamofire和ObjectMapper处理JSON转对象
date: 2015-07-27 16:57:32
tags:
- Swift
- 我
categories:
- Swift

---
#Alamofire是一个使用Swift语言写的一个网络库，操作很简单，结合ObjectMapper、AlamofireObjectMapper，可以轻松的将JSON数据转换为对象类型！

#Alamofire：https://github.com/Alamofire/Alamofire

#ObjectMapper：https://github.com/Hearst-DD/ObjectMapper

#ALamoObjectmapper：https://github.com/tristanhimmelman/AlamofireObjectMapper

#假设有个地址返回如下JSON数据：
## 代码块
{% codeblock .compact http://underscorejs.org/#compact Underscore.js %}
{
    "meta":
    {
        "total": "16868",
        "time": "0.435",
        "limit": "10",
        "offset": 0,
        "currency": "CNY",
        "symbol": "¥"
    },
    "products": [
    {
        "time_updated": "1432808931",
        "commission": "363.42",
        "price": "6,056.92",
        "sku": "517569",
        "upc": "",
        "pweight": null,
        "product_id": "82808bcd27eeb65dd15740aee40ec07c",
        "product_image": "http://images.rewardstyle.com/img?v=1.3&p=82808bcd27eeb65dd15740aee40ec07c",
        "product_type": "1",
        "product_name": "Cape-back crepe and chiffon mini dress",
        "advertiser": "NET-A-PORTER UK",
        "designer": "Gareth Pugh",
        "category": "WOMEN->Clothing->Dresses"
    },
    {
        "time_updated": "1432808931",
        "commission": "1,359.23",
        "price": "22,653.85",
        "sku": "549499",
        "upc": "",
        "pweight": null,
        "product_id": "e147fbbf30c65538b367f3a6ab539252",
        "product_image": "http://images.rewardstyle.com/img?v=1.3&p=e147fbbf30c65538b367f3a6ab539252",
        "product_type": "1",
        "product_name": "Woven suede tote",
        "advertiser": "NET-A-PORTER UK",
        "designer": "Loewe",
        "category": "WOMEN->Bags->Tote"
    }
                 ]
}

{% endcodeblock %}

#使用对象的概念来解读这段JSON数据，它包含Meta和Product：Meta是一个对象，Products是一个数组类型，包含了多个Product对象。

#我们首先使用ObjectMapper库定义的规则，来写一个实体类。
{% codeblock .compact http://underscorejs.org/#compact Underscore.js %}
class Meta : Mappable{
    var total:String?
    var time:Double?
    var limit:String?
    var offset:String?
    var currency:String?
    var symbol:String?
    init(){}
    required init?(_ map: Map){
        mapping(map)
    }
    func mapping(map: Map) {
        total <- map["total"]
        time <- map["time"]
        limit <- map["limit"]
        offset <- map["offset"]
        currency <- map["currency"]
        symbol <- map["symbol"]
    }
}


class Product : Mappable{
    
    var time_updated:String?
    var commission:String?
    var price:String?
    var sku:String?
    var product_id:String?
    var product_image:String?
    var product_name:String?
    var advertiser:String?
    var designer:String?
    var category:String?
    init(){}
    
    required init?(_ map: Map) {
        mapping(map)
    }
    
    func mapping(map: Map) {
        time_updated <- map["prtime_updated"]
        commission <- map["age"]
        price <- map["price"]
        sku <- map["sku"]
        product_id <- map["product_id"]
        product_image <- map["product_image"]
        product_name <- map["product_name"]
        advertiser <- map["advertiser"]
        designer <- map["designer"]
        category <- map["category"]
    }
}
{% endcodeblock %}

#每个实体类都实现了Mappable协议。这仅仅是JSON数据中的两个对象，现在我们要再声明下整个JSON数据的对象定义，它将作为顶层包含着Meta类和Product类：

{% codeblock .compact http://underscorejs.org/#compact Underscore.js %}

class ProductResponse:Mappable{
    var meta:Meta?
    var products:[Product]?
    
    init(){}
    required init?(_ map: Map) {
        mapping(map)
    }
    func mapping(map: Map) {
        meta <- map["meta"]
        products <- map["products"]
    }
}

{% endcodeblock %}
#接下来，就可以使用Alamofire来操作了。这时候，可以使用AlamofireObjectMapper库，它使用extension扩展了Alamofire的response方法：

{% codeblock .compact http://underscorejs.org/#compact Underscore.js %}

Alamofire.request(Method.GET, "请求的地址", parameters: nil, encoding: ParameterEncoding.URL).responseObject { (response:ProductResponse?, error:NSError?) -> Void in
    if let products = response?.products{
        for p in products{
            println(p.product_name)
        }
    }
}// end request
{% endcodeblock %}

