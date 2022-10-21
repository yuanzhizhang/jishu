# Lambda
***使用匿名内部类将行为和按钮点击进行绑定

```Java
	Runnable r = new Runnable(){
		@Override
		public void run(){
			Sysout.out.println("Hello");
		}
	}
```

上述使用匿名内部类的方法中，逻辑代码只有一行，但是不得不加上4行冗余的样板代码

```Java
	Runnable r = () -> Sysout.out.println("Hello")
```

与使用匿名内部类不同的是，使用Lambda表达式简洁代码的同时不需要显式地声明event参数，无需指定类型。因为javac会根据程序的上下文推断出event的类型。这意味着如果参数类型不言而明，则无需显示指定。

# 基础语法
#### 结构
> ->：Lambda操作符
> 左边：Lambda表达式的参数列表 （接口中抽象方法的参数）
> 右边：Lambda表达式中所需执行的操作符，即Lambda表达式 （对抽象方法的实现）

# 练习
```Java
	public static void printNum(IntPredicate predicate){
		int[] arr = {1,2,3,4,5,6,7,8};
		for(int i:arr){
			if predicate.test(i){
				System.out.println(i);
			}
		}
	}
	public static void main(String[] args){
		printNum(new IntPredicate(){
			@Override
			public boolean test(int value){
				return value%2=0;
			}
		});
	}
```

Lambda写法：
```Java
	public static void printNum(IntPredicate predicate){
		int[] arr = {1,2,3,4,5,6,7,8};
		for(int i:arr){
			if predicate.test(i){
				System.out.println(i);
			}
		}
	}
	public static void main(String[] args){
		printNum((int value) -> { return value%2=0});
	}
```

***泛型

```Java
	public static void main(String[] args){
		Integer result = typeConver(new Function<String,Integer>(){
			@Override
			pubilc Integer apply(String s) {
				return Integer.valueOf(s);
			}
		});
		System.out.println(result);
	}

	public static <R> R typeConver(Function<String,R> function){
		String str = "1234";
		R result = funcation.apply(str);
		return result;
	}
```

Lambda写法：
```Java
	public static void main(String[] args){
		Integer result = typeConver((String s) -> {return Integer.valueOf(s);});
		System.out.println(result);
	}

	public static <R> R typeConver(Function<String,R> function){
		String str = "1234";
		R result = funcation.apply(str);
		return result;
	}
```

# 省略规则
- 参数类型可省略
- 方法体只有一行代码时大括号、return、分号可以省略
- 方法只有一个参数时小括号可以省略