# Stream
可以被用来对集合或数组进行流式操作

## 创建流
单列集合： `集合对象.stream`
```Java
	List<Author> author = getAuthors();
	Stream<Author> stream = authors.stream();
```

数组： 