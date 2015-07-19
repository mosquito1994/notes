# 对象学习总结 #
1. 创建对象

 (1) 工厂模式
 
 	原理是在函数内部创建了一个对象并将它返回

 	```javascript
 	function Water(brand, contain) {
 		var o = new Object();
 		o.brand = brand;
 		o.contain = contain;
 		return o;
 	}
 	var Binglv = Water('Cocacola', 'bottle');
 	```

 (2) 构造函数模式
 
 	原理是将函数本身构造成一个对象

 	```javascript
 	function Water(brand, contain) {
 		this.brand = brand;
 		this.contain = contain;
 	}
 	var Binglv = new Water('Cocacola', 'bottle');
 	```

 (3) 原型模式

  	1) 原理是利用prototype扩展函数对象
  	
   	```javascript
   	function Water(){
  
   	}
   	Water.prototype.brand = 'Cocacola';
   	Water.prototype.contain = 'bottle';
   	```
   	Water.prototype.constructor 将会指向 Water函数
   	
   	或者使用字面量方法声明
   	
   	```javascript
   	Water.prototype = {
   		brand: 'Cocacola',
   		contain: 'bottle'
   	}
   	```
   	
   	这时Water.prototype.constructor将会指向Object而不是Water
   	
   	故需要在之前声明constructor: Water, 即：
   	
   	```javascript
   	Water.prototype = {
   		constructor: Water,
   		brand: 'Cocacola',
   		contain: 'bottle'
   	}
   	```
  
   2) 只使用原型模式的弊端
   	
 	 如果原型中的属性是数组形式，一旦创建实例之后向数组中加入新的属性时，原型中的属性也会改变
 	  
    ```javascript
    function Water(){

    }
    Water.prototype.brand = ['Cocacola'];
    Water.prototype.contain = 'bottle';
    var water2 = new Water();
    water2.brand.push('Kanshifu');
    console.log(Water.prototype.brand); // ['Cocacola', 'Kanshifu'];
    ```

 (4) 推荐的对象创建模式
 
 	私有方法使用构造函数方式声明，公有方法采用原型模式声明

2. 原型的动态性、字面量、重写

 (1) 动态性：即时在实例创建之后为原型添加新的方法/属性，实例中的方法/属性也会相应变化
 
 	```javascript
 	function Water(){

 	}
 	Water.prototype.brand = 'Cocacola';
 	Water.prototype.contain = 'bottle';
 	var water = new Water();
 	Water.prototype.type = 'pure';
 	Water.prototype.sayBrand = function() {
 		alert(this.brand);
 	}
 	water.sayBrand(); // alert('Cocacola');
 	console.log(water.type); // pure
 	```

 (2) 字面量：

 	```javascript
 	Water.prototype = {
 		constructor: Water,
 		brand: 'Cocacola',
 		contain: 'bottle'
 	}
 	```
 	
 	这种类似json格式的声明方法叫做字面量形式
 	
 	数组的字面量形式:
 	```javascript
 	var waterBrand = ['Cocacola', 'Kanshifu', 'Nongfushanquan'];
 	```
 	
 	在声明对象并创建实例之后，如果再使用字面量形式修改原型，则会重写字面量，实例将切断与现有字面量对象的一切关系，但仍然保留原来对象中的属性方法，即仍然引用原对象的属性方法
 
 (3) 重写

   	1) 使用字面量方法再次声明对象会重写对象
  
   	2) 将obj.prototype赋予其他原型的实例(即改变了指针指向)会重写对象，这将在继承中说明
   	
   	3) 原型模式和构造函数方法声明的属性方法都可以通过prototype方式重写，但是构造函数方法中声明的属性方法在重写后并不能改变对象中的对应属性方法。但是无论如何都可以通过直接给实例添加同名属性方法来修改对应值，这也称为属性屏蔽，将在后面提及。
   	
   	```javascript
   	function Water(){
   		this.brand = 'Cocacola';
   		this.contain = 'bottle';
   	}
   	Water.prototype = {
   		constructor: Water,
   		sayBrand: function() {
   			alert(this.brand);
   		}
   	}
   	var water = new Water();
   	Water.prototype.brand = 'Kangshifu';
   	Water.prototype.sayBrand = function() {
   		alert('It is from ' + this.brand);
   	}
   	water.sayBrand(); // alert('It is from Cocacola');
   	water.brand = 'Kangshifu';
   	water.sayBrand(); // alert('It is from Kangshifu');
   	```

3. 继承、for-in
 
 (1) \_proto\_
 
   	这其实是一个指针，在chrome等浏览器中给了这个名字。
   	
   	为什么实例可以访问到原型对象中的属性？
   	
   	因为实例中包含有指向原型对象的指针，继承的实现也是基于这一指针。
 
 (2) 继承
 	
  1) 基本思想：改变\_proto\_指针的指向，当一个原型对象中指针指向了需要继承的对象时，也即实现了继承。
   	
   	```javascript
   	function Father() {
   		this.name = 'father'，
   		this.age = 40
   	}
   	Father.prototype = {
   		constructor: Father,
   		sayName: function() {
   			alert(this.name);
   		}
   	}
   	function Son() {
  
   	}
   	Son.prototype = new Father(); // 继承Father的全部属性和方法
   	var son = new Son();
   	console.log(son instanceof Son); // true
   	console.log(son instanceof Father); // true
   	console.log(son instanceof Object); // true 
   	```
   	
   这里看到son是Object、Father、Son的实例。这个特点就是原型链的体现。
   	
   2) 原型链
   	
    - 原型链有点像函数中的作用域链
   
   	 就拿上面的例子来说，如果现在我在son的基础上调用方法toString()，即son.toString()
   	  
   	 结果是"[object Object]"，那么在得到结果的之前发生了什么样的事情呢？
   	  
   	 首先，在son中寻找toString方法，没有找到，于是顺着\_proto\_指针，向Son这一对象中寻找。还是没有找到，又顺着\_proto\_指针向Father寻找。最终在Object中找到了toString方法，将它返回。
   	 在寻找的过程中，形成了一条关系链，这条关系链就叫做原型链。所有对象和实例中的属性方法都是通过这样一条原型链来寻找并被调用的。
   	 
    - 属性屏蔽
   
   	 由于原型链的特点，可以通过给实例添加属性方法来防止对原型对象中同名属性方法的访问。
   	  
   	 对应的，javascript中也有相应的方法判断属性方法是否来自实例。接着上面的例子。
   
   	  ```javascript
       son.hasOwnProperty('name'); // false
       son.name = 'son';
       son.hasOwnProperty('name'); // true 
   	  ```
  
    3) 组合继承
   
      由于原型模式的继承会继承所有的属性方法，而在实际的使用中我们可能只是希望继承部分公共属性，这样就要用到组合继承。
      具体方法是，使用构造函数继承公共属性并写入私有属性，使用原型模式继承方法。
   
   	  ```javascript
   	  function Father() {
   	  	this.name = 'father';
   	  	this.face = 'round';
   	  	this.eyes = 'blue';
   	  }
   	  Father.prototype = {
   	  	constructor: Father,
   	  	kongfu: function() {
   	  		alert('I leaned it from Bruce Lee!');
   	  	}
   	  }
   	  function Son(face, eyes) {
   	  	constructor: Son;
   	  	Father.call(this, face, eyes);
   	  	this.name = 'son';
   	  }
   	  Son.prototype = new Father();
   	  Son.prototype.constructor = Son;
   	  var son = new Son();
   	  console.log(son); // Son {name: "son", face: "round", eyes: "blue", constructor: function, kongfu: function}
   	  ```
     
      可以看到，这样一种继承的方法是完全可以满足需求的。

   	4) 继承中的一些问题
     
     - 原型模式继承的问题
     
       仅使用原型模式会使实例继承原型对象所有的属性方法，而且如果属性中有数组形式的值，在实例中的增加数组项操作也会反映到原型中，使得一个实例改变就会影响全部实例。因此强烈建议使用构造函数法继承私有属性。
     
     - 为何指向实例实现继承而不是直接指向原型
     
      仍然看上面的例子，既然继承是将指针指向要继承的原型，那么为何是令
       
      ```javascript
     	Son.prototype = new Father();
      ```
       
      而不是直接
       
      ```javascript
     	Son.prototype = Father.prototype;
      ```
       
      基于上面的例子，我们先创建Father的一个实例father
       
      ```javascript
     	console.log(father);
      ```
       
       详细结果是这样的
       
      ```javascript
      Father {name: "father", face: "round", eyes: "blue", constructor: function, kongfu: function}
  		eyes: "blue"
  		face: "round"
  		name: "father"
  		__proto__: Father
     	```
     	
     	然后我们再把
     	
     	```javascript
   		Son.prototype = new Father();
     	```
     	
     	替换成
     	
     	```javascript
   		Son.prototype = Father.prototype;
     	```
     	
     	再新建一个Father的实例father看看是怎样的
     	
    	```javascript
     	Father {name: "father", face: "round", eyes: "blue", constructor: function, kongfu: function}
  		eyes: "blue"
  		face: "round"
  		name: "father"
  		__proto__: Son
     	```		
     	
  	  问题出来了，可以看到Father的指针指向了Son!
  	  也就是说，
  	  
    	```javascript
  	  Son.prototype = Father.prototype;
  	  ```
  	  
  	  这句使得Son.prototype和Father.prototype分别指向了对方，只要其中任意一个改变，另外一个也会变化，而在继承中我们显然不希望这样。
  	  
  	  但是，在规避a中情况下，
  	  
  	  ```javascript
  	  Son.prototype = new Father();
  	  ```
  	  
    	这里的指针会是单向的，不会影响到被继承原型中的属性方法，是一种安全的继承方法。
    	
  	  但是还有一点要注意的是，如果我们先创建了Father的实例father，然后通过
  	  
  	  ```javascript
    	Son.prototype = father;
    	```
    	
  	  实现继承，这样的话father又不对了
  	  
  	  ```javascript
  	 	Son {name: "father", face: "round", eyes: "blue", constructor: function, kongfu: function}
  			constructor: function Son(face, eyes) {
  			eyes: "blue"
  			face: "round"
  			name: "father"
  			__proto__: Father
    	```		
    	
  	  father成为了Son的实例吗?
  	  
    	```javascript
  	  father instanceof Son; // false
    	father instanceof Father; // true
    	```
    	
  	  但是如果我改变一下Son
  	  
    	```javascript
    	Son.prototype.name = 'son';
    	console.log(father.name); // son
    	```
    	
  	  应该是这样的操作使得Son.prototype和father中的指针分别指向了对方。
  	  所以，如果father这个实例还有继续使用的话，就不能用于继承。
    	因此，结论是
    	
  	  ```javascript
    	Son.prototype = new Father();
  	  ```
  	  
    	确实是最最安全的继承方法了，应当牢记它。

 (3) for-in遍历对象属性

 	for-in遍历对象属性的特点

   	1) 不会遍历不可枚举的属性
   	
   	2) 只要能从原型链中找到属性值就能读取，而不管属性是来自实例还是原型对象
