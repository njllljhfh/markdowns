

### 二、技术实现

1、前端int类型最高精度为15位，太高的数字会变掉，所以19位的id用字符串的格式

2、VUE/JS 的 image.onload() 事件是异步执行的，但是我们上传所有图片信息的时候需要等他们都执行完才可以。所以要等所有的 image.onload() 执行完才可以，有两种解决办法：

```javascript
# 1、利用定时器判断是否 list.length>=sum，在里面调用要执行的函数

# 2、利用在image.onload()中判断 list.length>=sum，来判断是否执行完所有的onload()事件
# 这个方法比较好，不耗性能，格式类似如下
for(i=0;i<k;i++){
    image.onload(
        list.push(xx)
        if(list.length>=sum){
        	callback()
    	}
    )
}
```

3、前端多选图片是一个对象 newValue列表，当 newValue[i].raw时，开始读取数据，

4、由于 image.onload响应完成的顺序是错乱的，所以当list.push的时候，其实不是根据当初读的顺序排的

```json
# js根据下标排序
存储列表的时候，按数字下标存储，自动排序

# 坑，这样下标为最后一个的加上时，长度就是最后的长度了
indexOf(null) 如果要检索的字符串值没有出现，则该方法返回 -1。(这个方法不行)，数组元素为空的，我们无法判断
在定义一个小列表辅助判断长度，小列表长度==sum && list.length==sum

```

> 参考：
>
> [JS 判断某变量是否为某数组中的一个值 的几种方法](https://www.cnblogs.com/suxiaBlogs/p/7142392.html)  
>
> [JavaScript indexOf() 方法](http://www.w3school.com.cn/jsref/jsref_indexOf.asp)