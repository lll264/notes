# form-serialize插件

# LocalStorage

LocalStorage主要由三个方法组成，简单来讲就是存、取、删三种操作对应的使用方法，具体如下所示：

1、存
localStorage.setItem(key, value)：将value存储到key字段，如果key存在时，就更新value。

2、取
localStorage.getItem(key)：获取指定key本地存储的值，如果key不存在就返回null。

3、删
删除对应了两种删除方式，一种是根据对应的key定向删除，一种是直接全部删除。
①根据key指定删除
localStorage.removeItem(key)：删除指定key本地存储的值，一经删除，key对应的数据将全部删除掉。
②全部删除
localStorage.clear()：该操作适合删除多个缓存数据，但是一旦执行就会清除所有的本地缓存数据。

4、其他方法

①遍历存储的key
localStorage.length：数据的总数。eg：localStorage.length

②获取key
localStorage.key(index)：获取对应的key。eg：let key = localStorage.key(index);

③存储JSON格式的数据
JSON.stringify(data) 将一个对象转换成JSON格式的字符串，返回转换后的字符串；
JSON.parse(data) 将数据解析成对象，返回解析后的对象。

但是考虑到localStorage存储的数据不能跨浏览器共享，所以只能读取当前浏览器的数据，而且存储空间为5M。

localStorage特点
localStorage既有优点也有劣势，具体如下所示：
localStorage拓展并且替代了cookie的功能，localStorage是一个普通对象，任何对象的操作都适用；
localStorage可以将第一次请求的数据直接存储到本地，而且浏览器支持的是5M大小；
浏览器只有在IE8以上的IE版本才支持localStorage；
localStorage的值类型目前仅限定为string类型，如果想要使用常用的JSON对象类型，则需要作转换；
localStorage在浏览器的隐私模式下是不能读取的；
爬虫爬取不到localStorage；
localStorage属于永久性存储；
localStorage要通过域名访问的方式才能起作用，如果调用clear()方法，那么key、oldValue和newValue都会被设置为null；
localStorage使用方式一致，localStorage没有时间限制的数据存储。