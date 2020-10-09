---
title: js设计模式
tags: JavaScript, 设计模式
---

# 1. 单例模式 singleton

1. 定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点

2. 实现：用变量标志当前的是否已经为该类创造过实例对象，如果创造过则直接返回该实例，否则创造一个实例返回

    ```JavaScript
    	let getSingle = (function() {
            let instance;
            return function (fn, ...rest) {     //fn: 用于创建单例的类
            	return instance || instance = fn.apply(this, rest);
            }
        })()
    ```

3. 惰性单例：在需要时才创建实例对象

# 2. 策略模式strategy

1. 定义：将不同情况的解决办法定义为函数，并用对象封装起来，不同情况调用不同函数
2. 目的：使算法的使用和实现分离，内部实现其功能，用户只用关心使用
3. 组成：
   - 策略类strategy： 封装了解决不同情况的对个算法(函数), 负责计算具体过程
   - 环境类context：接收用户的请求，并将请求委托(分发)给对应的策略，因此需要维持对策略类的引用
4. 举例：表单验证

```html
<html>
	<body>
		<form action="http:// xxx.com/register" id="registerForm" method="post">
			请输入用户名:<input type="text" name="userName"/ > 请输入密码:<input
			type="text" name="password"/ > 请输入手机号码:<input type="text"
			name="phoneNumber"/ >
			<button>提交</button>
		</form>
		<script>
            
/***********************策略对象**************************/
			var strategies = {
				isNonEmpty: function (value, errorMsg) {  // 传入表单值
					if (value === "") {
						return errorMsg;
					}
				},
				minLength: function (value, length, errorMsg) {
					if (value.length < length) {
						return errorMsg;
					}
				},
				isMobile: function (value, errorMsg) {
					if (!/(^1[3|5|8][0-9]{9}$)/.test(value)) {
						return errorMsg;
					}
				},
			};
/***********************Validator 类**************************/
			var Validator = function () {
				this.cache = [];   // 将需检查的规则全部缓存，不用考虑顺序
			};
            // 添加需检查的规则列表
			Validator.prototype.add = function (dom, rules) {
				var self = this;
				for (var i = 0, rule; (rule = rules[i++]); ) {
					(function (rule) {   // 为什么需要闭包？
						var strategyAry = rule.strategy.split(":");
						var errorMsg = rule.errorMsg;
						self.cache.push(function () {  //缓存规则检查前的信息加工函数
							var strategy = strategyAry.shift();
							strategyAry.unshift(dom.value);
							strategyAry.push(errorMsg);
							return strategies[strategy].apply(dom, strategyAry);
						});
					})(rule);
				}
			};
            // 依次调用cache的函数进行检查
			Validator.prototype.start = function () {
				for (var i = 0, validatorFunc;(validatorFunc = this.cache[i++]);) {
					var errorMsg = validatorFunc();
					if (errorMsg) {
						return errorMsg;
					}
				}
			};
            
            
/***********************客户调用代码**************************/
			var registerForm = document.getElementById("registerForm");

			var validataFunc = function () {
				var validator = new Validator();
                // 添加检查需检查的表单项
				validator.add(registerForm.userName, [
					{ strategy: "isNonEmpty", errorMsg: "用户名不能为空" },
					{
						strategy: "minLength:6",
						errorMsg: "用户名长度不能小于 10 位",
					},
				]);
				validator.add(registerForm.password, [
					{
						strategy: "minLength:6",
						errorMsg: "密码长度不能小于 6 位",
					},
				]);
				validator.add(registerForm.phoneNumber, [
					{ strategy: "isMobile", errorMsg: "手机号码格式不正确" },
				]);
				var errorMsg = validator.start();
				return errorMsg;
			};
            //绑定表单验证的事件
			registerForm.onsubmit = function () {
				var errorMsg = validataFunc();
				if (errorMsg) {
					alert(errorMsg);
					return false;
				}
			};
		</script>
	</body>
</html>

```

