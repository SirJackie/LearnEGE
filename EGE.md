1-6

```c++
initgraph(640, 480);
getch();
closegraph();
```

```c++
setcolor(EGERGB(0xFF, 0x0, 0x0));
setfillcolor(EGERGB(0xFF, 0x0, 0x80));
setbkcolor(EGERGB(0x0, 0x40, 0x0));  // Both Font and Bg
setfontbkcolor(EGERGB(0x80, 0x00, 0x80));  // Font only
setbkmode(TRANSPARENT);  //Font only
setbkmode(OPAQUE);  //Font only
```

```c++
// Line
line(0, 0, 1024, 576);

// Circle
circle(100, 100, 100);
fillcircle(100, 100, 100);

// Ellipse
ellipse(300, 300, 90, 180, 200, 100);
sector(300, 300, 90, 180, 200, 100);
fillellipse(300, 300, 200, 100);

// Rect
rectangle(10, 10, 100, 100);
bar(10, 10, 100, 100);
```

7

```c++
setfont(40, 0, "宋体");
outtextxy(30, 30, "Hello EGE 中文支持");
outtextrect(30, 80, 200, 100, "Hello EGE\n中文支持");
```

8

```c++
setviewport(150, 150, 300, 300, 1);
setviewport(0, 0, getwidth(), getheight(), 1);
cleardevice();
```

9-11

```c++
PIMAGE img = newimage();
PIMAGE img = newimage(80, 60);
getimage(img, 0, 0, 80, 60);
putimage(80, 60, img);
putimage(x * 160, y * 120, 160, 120, img, 0, 0, 80, 60);
delimage(img);
xxx(xxx, xxx, xxx, img);
```

```c++
//四种贴图比较
putimage(0, 0, img);
putimage_alphablend(NULL, img, 160, 0, 0x80); //半透明度为0x80
putimage_transparent(NULL, img, 0, 80, BLACK);        //透明贴图，关键色为BLACK，源图为这个颜色的地方会被忽略
putimage_alphatransparent(NULL, img, 160, 80, BLACK, 0xA0); //同时使用透明和半透明
```

```c++
color_t* buffer = getbuffer(img);
```

12

```c++
getch();
getkey();
kbhit();
kbmsg();
```

Help

```c++
在 include "graphics.h" 之前加上 #define SHOW_CONSOLE
```

13

```c++
setrendermode(RENDER_MANUAL); //不使用自动刷新，减少闪烁

int x, y;	
//获取鼠标坐标，此函数不等待。若鼠标移出了窗口，那么坐标值不会更新
//特殊情况是，你按着鼠标键不放，拖出窗口，这样坐标值会依然更新
mousepos(&x, &y);

is_run();
delay_fps(60);

mouse_msg msg = {0};
while (mousemsg())
{
	msg = getmouse();
}

xyprintf(0, 0, "x = %10d  y = %10d",
			msg.x, msg.y);

mousemsg:
x
当前鼠标 x 坐标
y
当前鼠标 y 坐标

is_move()
是否鼠标移动消息，类型为bool
is_down()
是否鼠标按键按下消息，类型为bool
is_up()
是否鼠标按键放开消息，类型为bool

is_left()
是否鼠标左键消息，类型为bool
is_mid()
是否鼠标中键消息，类型为bool
is_right()
是否鼠标右键消息，类型为bool

is_wheel()
是否鼠标滚轮滚动消息，类型为bool
wheel
鼠标滚轮滚动值，一般情况下为 120 的倍数或者约数。
```

14

```c++
inputbox_getline("标题", "文本框提示", str, 100);
```

15

```c++
settextjustify(horiz, vert);
// horiz 可以是 LEFT_TEXT (默认), CENTER_TEXT,  RIGHT_TEXT
// vert  可以是  TOP_TEXT (默认), CENTER_TEXT, BOTTOM_TEXT
```

16

```c++
fclock();
```

