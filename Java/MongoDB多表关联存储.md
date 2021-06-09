# 1. 数据关联存储
## 1.1. 一对多（数据量小）
这种情况可使用如下存储模型
```json
{
    name: 'Kate Monster',
    id: '123-456-7890',
    addresses : [
        { street: '123 Sesame St', city: 'Anytown', cc: 'USA' },
        { street: '123 Avenue Q', city: 'New York', cc: 'USA' }
    ]
}
```
### 1.1.1. 优点
你不需要执行单独的查询就可以获得某个Person的所有Address信息。
### 1.1.2. 缺点
你无法像操作独立文档那样来操作Address信息。你必须首先操作（比如查询）Person文档后，才有可能继续操作Address。另外，如果Adress是一类时，有可能你要每一个人对应的数据都要修改一遍，它不适合数据关联性较强的场景。
## 1.2. 一对多（数据量从几十到几百）
这种情况可使用如下存储模型  
从表：  
```json
{
    _id : ObjectID('AAAA'),
    partno : '123-aff-456',
    name : '#4 grommet',
    qty: 94,
    cost: 0.94,
    price: 3.99
}
```
主表：  
```json
{
    name : 'left-handed smoke shifter',
    manufacturer : 'Acme Corp',
    catalog_number: 1234,
    parts : [
        ObjectID('AAAA'), // reference to the #4 grommet above
        ObjectID('F17C'), // reference to a different Part
        ObjectID('D2AA'),
    ]
}
```
### 1.2.1. 优点
部件是作为独立文档（document）存在的，你可以对某一部件进行独立的操作，比如查询或更新。
### 1.2.2. 缺点
如上，你必须通过两次查询才能找到某一个产品所属的所有部件信息。这个缺点是可以接受的，本身实现起来也不难。而且，通过这种建模，你可以轻易的将1 to n扩展到n to n，即一个产品可以包含多个部件，同时一个部件也可以被多个产品所引用（即同一部件可以被多个产品使用）。
## 1.3. 一对多（数据量成千上万，或者更多）
这种情况可使用如下存储模型  
主表  
```json
{
    _id : ObjectID('AAAB'),
    name : 'goofy.example.com',
    ipaddr : '127.66.66.66'
}
```
从表   
```json
{
    time : ISODate("2014-03-28T09:42:41.382Z"),
    message : 'cpu is on fire!',
    host: ObjectID('AAAB') // Reference to the Host document
}
```
### 1.3.1. 优点
内存占用小，可分布加载。
### 1.3.2. 缺点
实现起来比较困难。不如前面两种方便。
# 2. 总结
1. n代表的数量级很小，且n代表的实体不需要单独操作时，可以采用嵌入式建模。
2. n代表的数量级比较大，或者n代表的实体需要单独进行操作时，采用在1中用Array存放引用的方式建模。
3. n代表的数量级非常大时，我们没有选择，只能在n端添加一个引用到1端。