#include<iostream>
#include<cstring>
#include<windows.h>
#include<conio.h>
#include<ctime>
#include<cstdlib>
using namespace std;
int i=0; int j=0;//刷图参数
class snack_map
{
private:
public:
	char ArrayA[20][20];//地图
	int x; int y;//食物参数
	snack_map() {

	}
	void mapStart(void) {
		while (i < 20 && j < 20) {
			while (i < 20) {
				ArrayA[i][j] = ' ';
				i++;
			}cout << endl;
			i = 0;
			j++;
		}j = 0;
		while (j < 20) {
			ArrayA[0][j] = '#';
			ArrayA[19][j] = '#';
			ArrayA[j][0] = '#';
			ArrayA[j][19] = '#';
			j++;
		}j = 0;
		ArrayA[10][10] = '@';//初始蛇头
		creat_food();//刷食物
	}
	void draw(void) {
		while (i < 20 && j < 20) {
			while (i < 20) {
				cout << ArrayA[i][j];
				i++;
			}cout << endl;
			i = 0;
			j++;
		}
		j = 0;
	}
	void creat_food(void) {
		do {
			x = rand() % 19 + 1;
			y = rand() % 19 + 1;
		} while (ArrayA[x][y] != ' ');
		ArrayA[x][y] = '$';
	}
};
snack_map mymap;
class snack_snack {
private:
	int q;//存蛇身参数
	char key;
public:
	bool keyInputed[(int)'z'];
	int tailX; int tailY;
	int headX; int headY;
	int ArrayS[20][20];
	snack_snack() {
		q = 1; key = 'w';
		tailX = 10; tailY = 10;
		headX = 10; headY = 10;
		while (i < 20 && j < 20) {
			while (i < 20) {
				ArrayS[i][j] = 0;
				i++;
			}
			i = 0;
			j++;
		}j = 0;
	}
	bool IsInput(char Key)
	{
		return keyInputed[(int)Key];
	}
	void direction() {
		char ch;
		if (IsInput('w')) {
			ch = 'w';
			key = ch;
		}
		else if (IsInput('a')) {
			ch = 'a';
			key = ch;
		}
		else if (IsInput('s')) {
			ch = 's';
			key = ch;
		}
		else if (IsInput('d')) {
			ch = 'd';
			key = ch;
		}
		else ch = key;

		switch (ch) {
		case'w':headY = headY - 1;
			break;
		case's':headY = headY + 1;
			break;
		case'a':headX = headX - 1;
			break;
		case'd':headX = headX + 1;
			break;
		default:break;
		}
		ArrayS[headX][headY] = q;
		q++;
	}
	void snack() {
		while (i < 20 && j < 20) {
			while (i < 20) {
				if (ArrayS[i][j] != 0) {
					mymap.ArrayA[i][j] = '.';
				}
				i++;
			}i = 0;
			j++;
		}j = 0;
	}
	void gettail() {
		int w = 520 * 1314;
		while (i < 20 && j < 20) {
			while (i < 20) {
				if (0 < ArrayS[i][j] && ArrayS[i][j] < w) {
					w = ArrayS[i][j];
					tailX = i; tailY = j;
				}i++;
			}i = 0;
			j++;
		}j = 0;
	}
};
snack_snack mysnack;
class snack_management {
private:
	int frame ;//帧率
	long start;
public:
	int n = 0;
	snack_management() {
		frame = 60;
		start = 0;
	}
	void HandleInput()
	{
		while (_kbhit())
		{
			int keyCode = (int)_getch();
			if (0 < keyCode && keyCode <= (int)'z')
				mysnack.keyInputed[keyCode] = true;
		}
	}
	bool IsUpdate() {
		if ((double)(clock() - start) / (double)CLOCKS_PER_SEC > 1.0 / (double)frame) {
			start = clock();
			return true;
		}
		else return false;
	}
	void InitInput()
	{
		for (int i = 0; i<(int)'z'; i++)
		{
			mysnack.keyInputed[i] = false;
		}
	}

	void IsEated() {
		if (mymap.ArrayA[mysnack.headX][mysnack.headY] == '#' || mymap.ArrayA[mysnack.headX][mysnack.headY] == '.') {
			system("cls");
			cout << "gg";
			cin.get();
		}
		else {
			if (mysnack.headX == mymap.x&&mysnack.headY == mymap.y) {
				mysnack.snack();
				mymap.ArrayA[mymap.x][mymap.y] = '@';
				mymap.creat_food();
			}
			else {
				mysnack.snack();
				mymap.ArrayA[mysnack.headX][mysnack.headY] = '@';
				mymap.ArrayA[mysnack.tailX][mysnack.tailY] = ' ';
				mysnack.ArrayS[mysnack.tailX][mysnack.tailY] = 0;
				mysnack.gettail();
			}
		}
	}
};
snack_management manager;
int main() {
	
	srand(time(0));
	mymap.mapStart();
	while(1){
		manager.n=manager.n+1;
		if (!manager.IsUpdate())
			continue;
		system("cls");
		manager.HandleInput();

		if (manager.n == 60) {//每次移动间隔
			mysnack.direction();
			manager.IsEated();
			manager.n = 0;
			manager.InitInput();
		}
		mymap.draw();
	} 
	return 0;
}
