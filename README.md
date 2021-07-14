```cpp
#include <iostream>
#include <limits.h>
using namespace std;

int main(){
	int n,m; 
	cin >> n >> m;
	int a[n][m];
	
	int tong = 0, hieu = INT_MIN;
	for(int i=0; i<n; i++){
		for(int j=0; j<m; j++){
			cin >> a[i][j];
			if(a[i][j] % 2 == 0) tong += a[i][j];
			else {
				if(hieu == INT_MIN) hieu = a[i][j];
				else hieu -= a[i][j];
			}
		}
	}
	cout << tong << " " << hieu;
}
```
