# 一些小的 javascript代码片段

#### 一.轮播

------

代码是一个同事让帮忙修改，实现一个页面可以多个轮播可以一起调用

###### 1.1 html部分代码

~~~html
<div class="team">
    <div class="box">
        <div class="xin3">
            <div class="x-box">
                <div class="x-box-t">
                    <div class="x-box-h">企业荣誉</div>
                    <div class="x-box-line"></div>
                </div>
                <div class="xin4">
                    <ul class="lilist" id="lunboli1">
                        <li>广东省产业园区协会副会长单位</li>
                        <li>深圳市产业园协会副会长单位</li>
                        <li>深圳市产业园区发展促进会副会长单位</li>
                        <li>广东省产业园区协会副会长单位</li>
                        <li>深圳市产业园协会副会长单位</li>
                        <li>深圳市产业园协会副会长单位</li>
                        <li>深圳市产业园协会副会长单位</li>
                    </ul>
                    <div class="libtn">
                        <div class="btn btnPrev" id="lbbtnP1"></div>
                        <div class="btnline"></div>
                        <div class="btn btnNext" id="lbbtnN1"></div>
                    </div>
                    <ul class="imglist" id="lunboimg1">
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                        <li>
                            <img src="images/x7.jpg">
                        </li>
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                        <li>
                            <img src="images/x7.jpg">
                        </li>
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                    </ul>
                </div>
                <div class="x-box-t">
                    <div class="x-box-h">企业荣誉</div>
                    <div class="x-box-line"></div>
                </div>
                <div class="xin4">
                    <ul class="lilist" id="lunboli2">
                        <li>广东省产业园区协会副会长单位</li>
                        <li>深圳市产业园协会副会长单位</li>
                        <li>深圳市产业园区发展促进会副会长单位</li>
                        <li>广东省产业园区协会副会长单位</li>
                        <li>深圳市产业园协会副会长单位</li>
                    </ul>
                    <div class="libtn">
                        <div class="btn btnPrev" id="lbbtnP2"></div>
                        <div class="btnline"></div>
                        <div class="btn btnNext" id="lbbtnN2"></div>
                    </div>
                    <ul class="imglist" id="lunboimg2">
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                        <li>
                            <img src="images/x7.jpg">
                        </li>
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                        <li>
                            <img src="images/x7.jpg">
                        </li>
                        <li>
                            <img src="images/x6.jpg">
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>
~~~

###### 1.2 初始的js代码

```javascript
<script>
	var showIndex = 0;
	var timer;
	$(function() {
		$("ul.imglist li").not(":eq(0)").css("display", "none");
		startTimer();

		$("ul.lilist li").hover(function() {
			clearInterval(timer);

			showIndex = $(this).index();
			showImg();
		}, function() {
			startTimer();
		});

		$(".btnNext").click(function() {
			clearInterval(timer);

			if(showIndex == 0) showIndex = 5;
			showIndex--;
			showImg();
			startTimer();
		});

		$(".btnPrev").click(function() {
			clearInterval(timer);

			if(showIndex == 4) showIndex = -1;
			showIndex++;
			showImg();
			startTimer();
		});
	});

	function startTimer() {
		timer = setInterval(function() {
			showIndex++;
			if(showIndex >= 5) showIndex = 0;
			showImg();
		}, 4000);
	}

	function showImg() {
		$("ul.imglist li").stop(true, true);
		$("ul.imglist li").hide().eq(showIndex).show();
		$("ul.lilist li").removeClass("on").eq(showIndex).addClass("on");
	}
</script>
```



​		**代码比较简单，就是使用一个定时器 定时移除 增加样式已达到对应的效果。**

​		**最终目标是  想要单个页面多个一起使用,并且可以自定义列表的li数量长度**

###### 1.3 封装成一个对象吧

​		**1、 多个对象怎么拷贝 使用了 {...abc} = Object ,很久没学习js所以去搜索知道了这样可以拷贝一个对象，比for循环，JSON 对象处理 简单多了，所以那些就不用记了。**

​		**2、箭头函数  例子 (a,b)=>{ return a+b },好处可以保留this指针指向的对象，简化了匿名函数的方法**

​		**3、改变函数的this指针的对象，对象调用，内部this指针是调用的对象，函数调用内部this指针都是window顶层对象，ES6增加了 let和const  可以声明只在代码段内有效的变量等等，apply这个方法改变函数的执行变量环境，以下代码中的 init 部分。** 

~~~javascript
		<script>
			
			var	lunbo =  {
				classLiName:'ul.lilist li', //列表li标签
				classImgName:"ul.imglist li",//图片li标签
				btnNext:".btnNext",//上一个按钮，因为你样式箭头是反的所以就这样了，本身应该是下一个的
				btnPrev:".btnPrev",//下一个按钮
				len : 5,
				showIndex:0,
				timer:{},
				init:function (obj,func)//改变函数执行环境
				{
					return function(){func.apply(obj,arguments);};
				},
				startTimer:function() {
					//使用箭头函数保持 this 对象 在定时器中不被改变
					this.timer = setInterval(res=>{
						// console.log(res,this);
						this.showIndex++;
						if(this.showIndex >= this.len) this.showIndex = 0;
						this.showImg(this);
					}, 4000);
				},
				showImg:function(){
					// console.log(this,res);
					$(this.classImgName).stop(true, true);
					$(this.classImgName).hide().eq(this.showIndex).show();
					$(this.classLiName).removeClass("on").eq(this.showIndex).addClass("on");
				},
				onload:function(res){
					let that = this;
					//初始化参数
					that.classLiName = res.classLiName || that.classLiName;
					that.classImgName = res.classImgName || that.classImgName;
					that.btnNext = res.btnNext || that.btnNext;
					that.btnPrev = res.btnPrev || that.btnPrev;
					that.len = res.len || that.len;
					that.timer = res.timer || that.timer;

					$(that.classImgName).not(":eq(0)").css("display", "none");
					// console.log(that);
					that.startTimer();
					$(that.classLiName).hover(function() {
						clearInterval(that.timer);
						that.showIndex = $(this).index();
						that.showImg();
					}, function() {
						that.startTimer();
					});

					//绑定按钮 同时改变调用对象为 当前轮播对象
					$(that.btnNext).click(that.init(that,that.next));
					$(that.btnPrev).click(that.init(that,that.prev));
					// $(that.btnPrev).click(that.prev(that));
				},
				next:function(){
					clearInterval(this.timer);
					if(this.showIndex == 0) this.showIndex = this.len;
					this.showIndex--;
					this.showImg();
					this.startTimer();
				},
				prev:function(){
					clearInterval(this.timer);
					if(this.showIndex == (this.len-1)) this.showIndex = -1;
					this.showIndex++;
					this.showImg();
					this.startTimer();
				}
			};
			//复制2个对象
			var { ...lunbo1 } = lunbo;
			var { ...lunbo2 } = lunbo;
			//加载数据
			lunbo1.onload({classLiName:"ul#lunboli1 li",classImgName:"ul#lunboimg1 li",btnNext:"#lbbtnN1",btnPrev:"#lbbtnP1",len:7});
			lunbo2.onload({classLiName:"ul#lunboli2 li",classImgName:"ul#lunboimg2 li",btnNext:"#lbbtnN2",btnPrev:"#lbbtnP2",len:5});

	</script>
~~~

