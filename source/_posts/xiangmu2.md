---
excerpt: 在原型控制与交互2课中，完成的关于p5.js的两个作业
title: 课业分享｜原型控制与交互2
date: 2020-05-5
tags: 项目
---
### 有关p5.js的学习
#### 课程作业分享1——“少去人多的地方”（游戏设计）
##### 作业展示

<p class="codepen" data-height="550" data-theme-id="light" data-default-tab="result" data-user="zhousm0929" data-slug-hash="QWjaGXX" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="QWjaGXX">
  <span>See the Pen <a href="https://codepen.io/zhousm0929/pen/QWjaGXX">
  QWjaGXX</a> by Zhousm0929 (<a href="https://codepen.io/zhousm0929">@zhousm0929</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

目的：人们想要避开病毒，但是病毒却是防不胜防的。戴口罩是一个办法，但是不同口罩保护的时效也是不同的。正如游戏所设置的那样，寻找病毒之间空间大的地方是关键，病毒越是密集，感染的风险越高，并且游戏界面越小，失败的可能性越大，所以我们要做的就是尽量避开人群去宽敞的空间。

操作：鼠标控制紫色的点，避开红蓝病毒点，得到黄色方块口罩就可能会有一段时间的保护。
##### 代码分享
```javascript
const colors = [
	"#F3447F", //粉色
	"#28A9CC", //蓝色
	"#F0DE41", //黄色
	"#A05EAF", //紫色
]
const grid = 20 //颜色固定

class rectangle {
	//构造函数
	constructor(x, y, r, i) {
		this.x = x;
		this.y = y;
		this.r = r;
		this.i = i
		this.angle = 1;
	}
	translating() {
		if (this.x < -windowWidth / 3 || this.x > windowWidth * 2 / 3 || this.y < -windowHeight / 2 || this.y > windowHeight /
			2) {
			this.x = cos(TAU / 50 * this.i) * this.r;
			this.y = sin(TAU / 50 * this.i) * this.r;
			//移动到屏幕外的点回到屏幕中间（在draw函数中移动过坐标中心点）
		} else {
			this.x += cos(TAU / 50 * this.i) * (this.r) / 50;
			this.y += sin(TAU / 50 * this.i) * (this.r + 20) / 50;
			//将点从中心向四周扩散
		}
	}
	rotating() {
		rotate(this.angle);
		this.angle += 1;
	}
	//定义rotating方法
	display() {
		fill("#F3447F")
		stroke("#F3447F");
		strokeWeight(1);
		ellipse(this.x, this.y, 7, 7);
	}
	//定义display方法（红色病毒）
	display2() {
		fill("#28A9CC")
		stroke("#28A9CC");
		strokeWeight(1);
		ellipse(this.x, this.y, 5, 5);
	}
	//定义display2方法（蓝色病毒）

	display3() {
		fill("#F0DE41")
		stroke("#F0DE41");
		strokeWeight(1);
		rect(this.x, this.y, 7, 7);
	}
    //定义display3方法（黄色口罩）
}


let rects = [];
let rects2 = [];
let mask = [];
//定义两个病毒和口罩的容器
let r;
//定义半径
const n = 150;
//角度的间隔
let agrid;
//定义角度
var hit = false
var hit2 = false
var hit3 = false
//定义撞击状态
var still = false
//定义口罩状态
let agrida
let agridc
let agridd
//定义中心部分角度
let ra = 0.1
let rc = 0.1
let rd = 0.2
//定义中心部分半径
let score = 0
//定义所得口罩数量
function setup() {
	Notiflix.Confirm.Show('前方高能', '是你吗！平平无奇游戏小天才', '当然', '我怕', function() { // Yes button callback 
			alert('游戏规则：勿碰红蓝点，鼠标控紫点；吃到黄口罩，命又多一条！');
		},
		function() { // No button callback 
			alert('这么简单你都不行？');
		});
		//提示框设置
	createCanvas(windowWidth, windowHeight);
	frameRate(25)
	agrid = TAU / 50
	for (let i = 0; i < n; i++) {
		r = (pow(random(1), 0.5) * 30) / 2;
		//定义随机半径
		let x = cos(agrid * i) * r;
		let y = sin(agrid * i) * r;
		let x2 = cos(agrid * i) * r * 2;
		let y2 = sin(agrid * i) * r * 2;
		//每个病毒的初始位置
		rects.push(new rectangle(x, y, r, i));
		rects2.push(new rectangle(x2, y2, r, i));
		rects[i].display()
		rects[i].display2()
		//实例化病毒并放到列表中
	}
	for (let i = 0; i < 10; i++) {
		let x0 = random(-windowWidth / 3, windowWidth / 4)
		let y0 = random(-windowHeight / 2, 0)
		//口罩随机生成的区域
		mask.push(new rectangle(x0, y0, r, i))
		mask[i].display3()
		//实例化口罩并放到列表中
	}
}


function draw() {
	background(255);
	push()
	translate(windowWidth / 3, windowHeight / 2);

	for (let i = 0; i < n; i++) {
		rects[i].translating()
		rects2[i].translating()
		rects[i].display()
		rects2[i].display2()
		hit = collideCircleCircle(mouseX - windowWidth / 3, mouseY - windowHeight / 2, 15, rects[i].x, rects[i].y, 7)
		hit2 = collideCircleCircle(mouseX - windowWidth / 3, mouseY - windowHeight / 2, 15, rects2[i].x, rects2[i].y, 5)
		//检测与病毒的撞击
		if (hit || hit2) {
			if (still) {
				agrida = TAU / 20
				fill("#F0DE41")
				noStroke()
				push()
				scale(ra)
				//放大半径
				ra += 0.02
				if (ra >= 5) {
					ra = 5
				}
				pop()
				for (let i = 0; i < 20; i++) {
					let x = cos(agrida * i) * 16
					let y = sin(agrida * i) * 16
					let a = x + mouseX - windowWidth / 3
					let b = y + mouseY - windowHeight / 2
					//注意坐标原点改过
					ellipse(a, b, 5 - ra, 5 - ra)
					//圆会逐渐减小
				}
				//有保护膜，碰到病毒，外壳碎的效果
				setTimeout(function() {
					still = false
				}, 5000);
				//使下一步检测延迟5秒，在保护膜破碎的时候还并不会输
            }
            else {
			  Notiflix.Report.Failure('You Lose!', '"看来你是平平无奇游戏小辣🐔"', '原谅一次');
			  //没有保护膜并碰病毒弹出对话框
			}

		}

	}

	for (let i = 0; i < 10; i++) {
		mask[i].display3()
		hit3 = collideRectCircle(mask[i].x, mask[i].y, 7, 7, mouseX - windowWidth / 3, mouseY - windowHeight / 2, 15)
		//检测是否碰到口罩
		if (hit3) {
			still = true
			//碰到口罩改变still状态
			mask[i].x = random(-windowWidth / 3, windowWidth / 4)
			mask[i].y = random(-windowHeight / 2, 0)
			//碰到一个将生成另一个口罩
			score += 1
			//分数加一
		}
		hit3 = false
		if (still) {
			noFill()
			stroke("#F0DE41")
			ellipse(mouseX - windowWidth / 3, mouseY - windowHeight / 2, 31, 31)
			//画保护膜
			setTimeout(function() {
				still = false
			}, 4000);
			//延迟4秒改变口罩状态

		}
	}
    pop()

	fill(255)
	noStroke()
	ellipse(windowWidth / 3, windowHeight / 2, 80, 80)
	//画白色圈

	fill("#A05EAF")
	ellipse(mouseX, mouseY, 15, 15)
	//画鼠标的圈

	fill("#F3447F")
	ellipse(windowWidth / 3, windowHeight / 2, 40, 40)
    //画中心点

	agridc = TAU / 18
	fill("#28A9CC")
	push()
	scale(rc)
	rc += 0.05
	if (rc >= 6.5) {
		rc = 0.1
	}
	pop()
	for (let i = 0; i < 18; i++) {
		let x = cos(agridc * i) * 30
		let y = sin(agridc * i) * 30
		let a = x + windowWidth / 3
		let b = y + windowHeight / 2
		ellipse(a, b, 6.5 - rc, 6.5 - rc)
	}
    //画中心蓝色部分


	agridd = TAU / 30
	fill("#F3447F")
	push()
	scale(rd)
	rd += 0.05
	if (rd >= 6) {
		rd = 0.1
	}
	pop()
	for (let i = 0; i < 30; i++) {
		let x = cos(agridd * i) * 40
		let y = sin(agridd * i) * 40
		let a = x + windowWidth / 3
		let b = y + windowHeight / 2
		ellipse(a, b, 6 - rd, 6 - rd)
	}
	//画中心红色部分
	textSize(14);
	fill(0)
	noStroke()
	strokeWeight(1)
	text("口罩所得数:" + score, windowWidth * 7.8 / 10, windowHeight * 0.5 / 10)
	//写出口罩所得数和分数
}
```

#### 课程作业分享2——“杭州亚运会”（海报设计）
##### 我的思考与准备
以下为我思考时的手稿
[![Yi6We1.png](https://s1.ax1x.com/2020/05/05/Yi6We1.png)](https://imgchr.com/i/Yi6We1)
以下为我查找到的资料
* 杭州亚运城市行动推进大会昨日召开
http://news.hexun.com/2020-04-18/201070072.html
* 亚运会如何提升一座城
http://zjtyol.zjol.com.cn/zjrd/202004/t20200420_11895119.shtml
* 杭州：践行绿色智能节俭文明 亚运场馆建设有序推进
https://baijiahao.baidu.com/s?id=1664452420823771453&wfr=spider&for=pc


##### 我的设计
我选择的是亚运会的主题，
想要从亚运会的四个理念“绿色、智能、文明、节俭”出发，
以交互式海报的形式，
表达出杭州与亚运会的关系。
杭州为亚运会所作出的建设其实反过来也相当于是亚运会给杭州带来的。

在海报中的交互形式是我们身为建筑者所做一些符合亚运会理念“绿色、智能、节俭”的事，拆除污染源和智能化建设以及利用废弃建筑材料制造混合物来建造亚运会场馆。
当我们完成所有的建设之后，就会发现杭州发生了巨大的变化，通过对比深化我想表达的主题思想，亚运会为杭州带来了活力文明绿色智能。

细节思考：
* 两张海报我各选用了紫色和橙色的对比色，因为我觉得暗紫色有一种压抑沉闷之感，而橙色却彰显活力。
* 在亚运会的理念“文明”方面，我觉得一个是在于人的文明，还有一个则是历史文明。
首先我觉得承办亚运会等世界级的盛事，给那所城市的人内心的民族自信心会提升很多，从而变得文明，在前一张海报中一群人挤着无法进入，而后一张人们井然有序已经成为了美化美化城市的一部分了。
在历史文明方面，我觉得将污染破坏源解决，反过来便会使得我们的文化历史完善的保存，所以在后一张海报中我加入了杭州的特色历史文化——良渚文化元素。
* “绿色”方面，我想到了习近平总书记的“绿水青山就是金山银山”当我们解决掉了污染源后，一些青山绿水自然人文景观便显露了出来。

##### 过程以及ai初稿展示
海报1的设计思路（参考图片）
[![Yi2VRU.png](https://s1.ax1x.com/2020/05/05/Yi2VRU.png)](https://imgchr.com/i/Yi2VRU)
海报2的设计思路（参考图片）
[![Yi2PZn.png](https://s1.ax1x.com/2020/05/05/Yi2PZn.png)](https://imgchr.com/i/Yi2PZn)
ai初稿对比图展示
[![YiXK3Q.png](https://s1.ax1x.com/2020/05/05/YiXK3Q.png)](https://imgchr.com/i/YiXK3Q)
##### 成果展示

##### 代码分享

### 课程感悟


