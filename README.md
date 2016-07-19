Anakonda
=========================

第一步
-------------------------

首先通过for循环在页面上打印出table表格，通过样式表去设置单个td的高度（15px）和宽度（15px）,同时将表格进行居中。

    function init() {
  		document.write("<table border='1'>")
  		for (var i = 0; i < 20; i++) {
  		document.write("<tr>");
  			for (var j = 0; j < 40; j++) {
  				document.write("<td></td>");
  			}
  			document.write("</tr>");
  		}
  		document.write("</table>");
  	}
  			
第二步
-------------------------

通过for循环在页面加载完成之后将所有的td放置在一个二维数组中（每个td在二维数组中的位置可以通过(i * 40 +j)得到），这样整个背景就完成了。

    window.onload = function() {
  		var tds = document.getElementsByTagName("td");
  	  for (var i = 0; i < 20; i++) {
  	    snakeArr[i] = new Array();
  		  for (var j = 0; j < 40; j++) {
  		  	snakeArr[i][j] = tds[(i * 40) + j];
    		}
      }
  	  food();
		  draw();
  	}
	
第三步
-------------------------	

初始化生成蛇的坐标（横、纵坐标分别用一个一维数组进行存储），然后将蛇所在的横纵坐标进行背景颜色的填充，这样在页面上就生成了一条蛇。其中后面所生成的食物也是通过下面的draw()函数进行背景颜色的填充。
    
    var xSnake = [2, 3, 4, 5];
	  var ySnake = [3, 3, 3, 3];
    
    function draw() {
			var color = new Array("chocolate", "orange", "yellow", "green", "blue", "lightblue", "purple");
			for (var i = 0; i < ySnake.length - 1; i++) {
				snakeArr[ySnake[i]][xSnake[i]].style.backgroundColor = color[parseInt(Math.random() * 6)];
			}
			snakeArr[ySnake[ySnake.length - 1]][xSnake[xSnake.length - 1]].style.backgroundColor = "red";
			snakeArr[yFood][xFood].style.backgroundColor = "black";
		}
		
第四步
-------------------------		

食物坐标的随机生成。需要生成两个在指定范围内的随机整数（0 <= x <= 39, 0 <= y <= 19）,同时要求食物的坐标不能与蛇那个时刻的坐标相同，因此要对所生成的坐标与蛇那个时刻的坐标进行对比，如果相同的话，则重新生成两个随机数，直到食物的坐标与蛇的坐标不同为止。

    var xFood;
		var yFood;
		
		function food() {
			var flag = true;

			do {
				xFood = parseInt(Math.random() * 40);
				yFood = parseInt(Math.random() * 20);
				for (var i = 0; i < xSnake.length; i++) {
					if (!((xFood == xSnake[i]) && (yFood == ySnake[i]))) {
						flag = false;
					}
				}
			} while (flag)
		}

第五步
-------------------------		

对按键进行判断。通过window.onkeydown来接收事件，不同的按键去执行不同的函数（moveDown()，moveUp(),moveLeft(),moveRight()）。


    window.onkeydown = function(event) {				
  	  	if (event.code == "ArrowDown" && lastKey != "ArrowUp") {
  		    moveDown()；
    		  lastKey = event.code;
    		} else if (event.code == "ArrowUp" && lastKey != "ArrowDown") {
    		  moveUp();
    			lastKey = event.code;
    		} else if (event.code == "ArrowRight" && lastKey != "ArrowLeft") {
    			moveRight();
    			lastKey = event.code;
    		} else if (event.code == "ArrowLeft" && lastKey != "ArrowRight") {
    		  moveLeft();
    		  lastKey = event.code;
    		}
  	}
	
第六步
-------------------------		

对蛇的每次移动进行边界检查。由于我将游戏规则设置成蛇能够穿墙--即蛇头移出边界后将在同行另一边的边界出现。因此要对蛇的坐标进行检查，判断其是否正在穿墙，当在进行穿墙时，将蛇头坐标进行修改。


    function check() {
			if(xSnake[xSnake.length - 1] < 0){
				xSnake[xSnake.length - 1] = 39;
			}else if(ySnake[ySnake.length - 1] < 0){
				ySnake[ySnake.length - 1] = 19;
			}else if(xSnake[xSnake.length - 1] > 39){
				xSnake[xSnake.length - 1] = 0;
			}else if(ySnake[ySnake.length - 1] > 19){
				ySnake[ySnake.length - 1] = 0;
			}
			return true;
		}
		
第七步
-------------------------	

判断蛇是否吃到了食物。即将食物的坐标与蛇头的坐标进行比较，如果蛇头下一步的坐标为食物的坐标，则将食物的坐标加入到蛇的坐标中去，蛇的长度加一，同时去除食物的背景颜色，重新生成新的食物坐标，并对颜色进行填充。从不同方向上吃到食物的判断条件有一定的区别，但思想都是一致的。下面给出的是向右移动吃到食物的代码。其中为了统计分数用了jQuery中的$("#id").html()来对分数进行修改。
通过对延迟时间的改变，可以对蛇的移动速度进行调整，规定初始时0.5s移动一格，蛇的长度每增加一，延迟时间变为原来的0.9倍，当延迟时间小于0.1s时，将延迟时间置为0.1s。


    function moveRight() {
			clearInterval(si);
			si = setInterval(function() {
			  var x = xSnake[xSnake.length - 1];
  			if (((x + 1) == xFood || ((x == 0)&&(x == xFood))) && ySnake[ySnake.length - 1] == yFood) {
  				xSnake.push(xFood);
  				ySnake.push(yFood);
  				$("#score").html(parseInt($("#score").html()) + 1);
  				food()；
  				if (delay > 100) {
  					delay = delay*0.9;
  				} else {
  		  		delay = 100;
  				}
  			} else {
  			  for (var i = 0; i < xSnake.length - 1; i++) {
  				  xSnake[i] = xSnake[i + 1]；
  				  ySnake[i] = ySnake[i + 1];
  			  }
  			  xSnake[xSnake.length - 1] = x + 1;
		    }
			  check();
			  remove();
			  draw();
			},delay);
		}


补充：
------------------

这个游戏不会存在Game Over,只有你想玩，你可以将整个表格填满。导致这个原因是并没有对游戏在何种情况下结束做出明确的规定，游戏中你可以穿墙，同时你也可以穿过自己的身体。
