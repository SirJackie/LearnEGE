大佬您好，kbhit()和kbmsg()在小熊猫DevCpp上无法正常使用。
表现为kbhit()和getch()联用时无反应，代码如下：

```c++
#include <graphics.h>
#include <stdio.h>
int main()
{
	initgraph(640, 480);
	outtextxy(0, 0, "please press any key");

	int k;
	for (;;)
	{
		if ( kbhit() ){
			k = getch();
			cleardevice();
		}
	}

	closegraph();
}
```

还有，inputbox_getline()的文本框提示部分不会显示，代码如下：

```c++
//用户交互——字符串数据输入* （非重点）
#include <graphics.h>

#include <stdio.h>

int main()
{
	initgraph(640, 480);
	
	//用来接收输入
	char str[100];
	
	//调用对话框函数
	inputbox_getline("标题", "文本框提示", str, 100);
	
	//显示输入的内容
	outtextxy(0, 0, str);
	
	getch();
	
	closegraph();
	return 0;
}
```

还有，这个demo无法正常运行：

```c++
#define SHOW_CONSOLE
#include "graphics.h"
#include <vector>
#include <string>
#include <cstdlib>
#include <direct.h>

#define MAX_IMAGES 100

using namespace std;

typedef struct Element
{
	Element(PIMAGE img = nullptr) : pimg(img), centerX(random(100) / 100.0f), centerY(random(100) / 100.0f), x(100.0f), y(100.0f), dx(random(6.0f) + 2.0f), dy(random(6.0f) + 2.0f), rot(random(100) / 100.0f), dRot(random(100) / 1000.0f), scaling(1.0f), dScaling(0.99f) {}
	PIMAGE pimg;
	float centerX, centerY;
	float x, y;
	float dx, dy;

	float rot, dRot;
	float scaling, dScaling;
}Element;

std::vector<Element> readMultiFileNameDlg(HWND hwnd, const char* title)
{
	vector <Element> images;
	vector<char> buffer;
	buffer.resize(4096);
	buffer[0] = 0;

	OPENFILENAMEA ofn;
	memset(&ofn, 0, sizeof(ofn));
	ofn.hwndOwner = hwnd;
	ofn.lStructSize = sizeof(ofn);
	ofn.Flags = OFN_EXPLORER | OFN_ALLOWMULTISELECT;
	ofn.lpstrFile = buffer.data();
	ofn.nMaxFile = 4096;
	ofn.lpstrFilter = "Image Files(*.jpg;*.png;*.bmp;*.gif)\0*.jpg;*.jpeg;*.png;*.bmp;*.gif\0All Files(*.*)\0*.*\0\0";

	ofn.lpstrTitle = title;
	if (!GetOpenFileNameA(&ofn))
		return images;

	const char* openFileNames = buffer.data();
	int offset = ofn.nFileOffset;

	buffer[offset - 1] = '\0';
	_chdir(openFileNames);
	
	openFileNames += offset;
	while (*openFileNames)
	{
		string tmp = buffer.data();
		tmp += "\\";
		tmp += openFileNames;
		while (*openFileNames++);

		PIMAGE pimg = newimage();
		
		if (getimage_pngfile(pimg, tmp.c_str()) == 0 || getimage(pimg, tmp.c_str()) == 0)
		{
			Element e;
			e.pimg = pimg;

			//对导入图片进行缩小操作, 防止图片过大导致卡顿.
			float scaling = max(getwidth(pimg) / 100.0f, getheight(pimg) / 100.0f);
			if (scaling > 1.0f)
			{
				PIMAGE resizeImage = newimage(getwidth(pimg) / scaling, getheight(pimg) / scaling);
				//将大图绘制到小图上。
				putimage(resizeImage, 0, 0, getwidth(resizeImage), getheight(resizeImage), pimg, 0, 0, getwidth(pimg), getheight(pimg));
				e.pimg = resizeImage;
				delimage(pimg); //使用重置大小之后的图片， 释放原始大小图片
			}

			images.push_back(e);			
		}
		else
		{
			printf("加载图片 %s 失败!\n", tmp.c_str());
			delimage(pimg);
		}
	}

	return images;
}

void addSimplePic(vector<Element>& v, int w, int h)
{
	int hw = w / 2, hh = h / 2;
	PIMAGE pimg = newimage(w, h);
	cleardevice(pimg); //清除pimg

	setcolor(0xffff0000 + random(0xffff), pimg);
	setfillcolor(0xffff0000 + random(0xffff), pimg);
	ege_fillrect(hw, hh, hw, hh, pimg);
	
	setcolor(0xffff0000, pimg);
	setfillcolor(0xff00ff00 + random(0xffff), pimg);
	ege_fillellipse(hw * 0.34f, hh * 0.34f, hw * 1.66f, hh * 1.66f, pimg);

	setcolor(0xff00ff00, pimg);
	setfillcolor(0xff0000ff + random(0xffff00), pimg);
	ege_fillellipse(hw * 0.66f, hh * 0.66f, hw * 1.34f, hh * 1.34f, pimg);

// 	int bufferLen = w * h;
// 	color_t* buffer = getbuffer(pimg);
// 	//若使用的EGE函数不绘制alpha通道， 则在这里进行填充.
// 	for (int i = 0; i != bufferLen; ++i)
// 	{
// 		color_t& c = buffer[i];
// 		if (c != 0) c |= 0xff000000;
// 	}

	Element e;
	e.pimg = pimg;
	v.push_back(e);
}

bool dealMsg(vector<Element>& imgs)
{
	while (kbhit())
	{
		int c = getch();
		switch (c)
		{
		case key_esc:
			return false;
		case key_space: case key_enter:
		{
			auto&& result = readMultiFileNameDlg(getHWnd(), "请选择多张图片!!");
			if (result.size() != 0)
			{
				if (imgs.size() + result.size() > MAX_IMAGES && imgs.size() != MAX_IMAGES)
				{
					size_t sz = min(result.size(), MAX_IMAGES - imgs.size());
					imgs.insert(imgs.end() - 1, result.begin(), result.begin() + sz);
					puts("图片数达到最大值!");
				}
				else
				{
					imgs.insert(imgs.end() - 1, result.begin(), result.end());
					puts("图片添加完毕!\n");
				}
			}
		}
			break;
		case 'a': case 'A':
		{
			if (imgs.size() < MAX_IMAGES)
			{
				int sz = random(50) + 30;
				addSimplePic(imgs, sz, sz);
				puts("添加随机图像!\n");
			}
			else
				puts("图片数达到最大值!");
			
		}
			break;
		default:
			break;
		}
	}

	return true;
}

int main()
{
	static const int W = 800, H = 600;

	vector<Element> imgs;
	initgraph(W, H, INIT_RENDERMANUAL);
	setbkmode(TRANSPARENT);
	setcolor(WHITE);

	addSimplePic(imgs, 100, 100);

	// is_run() 表示窗口绘图上下文存在, 当点击窗口右上角的叉叉的时候会返回false(大概)
	for (; is_run(); delay_fps(60))
	{
		if(!dealMsg(imgs))
			break;

		cleardevice();
		for (Element& e : imgs)
		{
			//慢速操作
			putimage_rotatezoom(nullptr, e.pimg, e.x, e.y, 0.5f, 0.5f, e.rot, e.scaling, 1, 0xc0, 1);

			//putimage_withalpha(nullptr, e.pimg, e.x, e.y); //这个版本的带alpha半透明， 但是无法进行旋转等操作

			e.x += e.dx;
			e.y += e.dy;
			e.rot += e.dRot;
			e.scaling *= e.dScaling;

			if (e.x < 0 || e.x > W)
				e.dx = -e.dx;
			if (e.y < 0 || e.y > H)
				e.dy = -e.dy;
			if (e.scaling < 0.5f || e.scaling > 2.0f)
				e.dScaling = e.scaling > 1.0f ? 0.99f : 1.01f;

		}

		{
			int x, y;
			mousepos(&x, &y);
			outtextxy(x - 100, y - 10, "按下空格键添加随机图形, 按A键添加图片!");
		}
	}

	for (auto& e : imgs)
	{
		delimage(e.pimg); //记得收尾， 养成良好习惯
	}

	imgs.clear();

	closegraph();
	return 0;
}
```

附上小熊猫DevCpp和操作系统的版本号：
小熊猫Dev-Cpp7 版本：beta.0.10.3 GCC 11.2
Windows 10 专业版 版本 2004 (操作系统内部版本 19041.1415)

求解决方案，谢谢！