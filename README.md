# pcodes

#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstring>
#include <string>
using namespace std;
bool isfirst = true;
char map[102][26];
void init(void){
	cout << "init func is called" << endl;
	for (int i = 0; i < 102; i++){
		map[i][0] = '$';
	}
	cout << "map updated" << endl;
}
int isPresent(int hkey, char key[], int size){
	int i = 0;
	if (map[hkey][0] == '$'){
		cout << "this position is free" << endl;
		return 0;
	}
	for (i = 0; i < size; i++){
		if (map[hkey][i] != key[i]){
			return 1;
			cout << "but this position is full" << endl;
		}
	}
	//if (map[hkey][i] != '0')
		//return 1;
	cout << "redundancy found" << endl;
	return 2;
}
int resolveCollisions(int hkey, char key[], int size){
	int j = 1;
	for (; j <= 19; j++){
		int new_key = (hkey + (j*j) + (23 * j)) % 101;
		int code = isPresent(new_key, key, size);
		if (code != 1){
			return new_key;
		}
	}
	return -1;
}
int HashKey(char key[], int size){
	int h_key = 0;
	for (int i = 0; i < size; i++){
		h_key += (((int)key[i])*(i + 1));
	}
	h_key *= 19;
	int hash_key = h_key % 101;
	cout << "inside hash key function, calculated value is " << hash_key << endl;
	int code = isPresent(hash_key, key, size);

	if (code == 1){
		int next_best = resolveCollisions(hash_key, key, size);
		return next_best;
	}

	else if (code == 0){
		cout << "hash value " << hash_key << " confirmed. exiting with code == 0" << endl;
		return hash_key;
	}

	else{
		cout << "gonna return -1" << endl;
		return -1;
	}
}
void addWord(int hash_key, char key[], int size){
	if (hash_key != -1){
		for (int i = 0; i < size; i++){
			map[hash_key][i] = key[i];
		}
	}
}
void delWord(int hash_key, char key[], int size){
	int code = isPresent(hash_key, key, size);
	if (code == 2){
		map[hash_key][0] = '$';
	}
}
int outputNum(char out[102][30], int num){
	int size = 1;
	if (num / 100 == 1){
		size = 3;
		out[num][0] = (char)(49);
		out[num][1] = (char)(48);
		out[num][2] = (char)(48 + (num % 100));
		out[num][3] = ':';
		return 4;
	}
	else if (num / 10 > 0){
		out[num][0] = (char)(48 + (num / 10));
		out[num][1] = (char)(48 + (num % 10));
		out[num][2] = ':';
		return 3;
	}
	while (num / 10){
		out[num][0] = char(48 + num);
		out[num][1] = ':';
		return 2;
	}
}
void processThis(char *operation, int size)
{
	if (isfirst == true){
		init();
		isfirst = false;
	}
	/*if (size == 4){
		char str[1];
		str[0] = ' ';
	}*/
	//else{
		char str[30];
		int i = 4;
		for (; i < size; i++){
			str[i - 4] = operation[i];
		}
		str[i] = '\0';

		cout << "going to calculate hash key of " << str << endl;
		int hash_key = HashKey(str, size-4);
		if (operation[0] == 'A' && hash_key > 0){
			addWord(hash_key, str, size - 4);
		}
		else{
			if (hash_key == -1){
				delWord(hash_key, str, size - 4);
			}
		}
	//}
	return;
}

int storeOutput(char out[102][30])
{
	int ans = 0;
	for (int i = 0; i < 102; i++){
		if (map[i][0] != '$'){
			ans++;
			int next_index = outputNum(out, i);
			for (int j = 0; j < 30; j++){
				if (map[i][j])
					out[i][next_index + j] = map[i][j];
				else
					out[i][next_index + j] = NULL;
			}
		}
	}
	isfirst = true;
	return ans;
}
int main()
{
	int T, n;
	string operation;
	//cout << "program started \n";
	freopen("input.txt", "r", stdin);
	freopen("output.txt", "w", stdout);
	cin >> T;
	while (T--)
	{
		//cout << "test case no " << T << endl;
		cin >> n;
		//cout << "n is " << n << endl;
		while (n--)
		{
			cin >> operation;
			cout << operation << " is the string" << endl;
			processThis((char*)operation.c_str(), operation.size());
		}
		char out[102][30];
		int c = storeOutput(out);

		cout << c << endl;
		for (int i = 0; i < c; ++i){
			cout << out[i] << endl;
		}
	}
	return 0;
}
