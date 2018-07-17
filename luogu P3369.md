# 平衡树 [Luogu P3369](https://www.luogu.org/problemnew/show/P3369)  
```
#include <iostream>
#include <cstdio>
#include <cctype>

int random() {
	static unsigned long long spot = 19260817;
	return spot = (spot * 10398329 + 214351234) % 2147483647;
}

struct Node {
	int key;
	int rand;
	int size;
	int left,right;
	Node (int key = 0): key(key),rand(random()),size(1),left(0),right(0) {}
} node[1000010];

inline int read() {
	register int x;
	register char c;
	bool f = false;
	while(!isdigit(c = getchar())) {
	if(c == '-') f = true;	
	}
	x = c ^ 48;
	while(isdigit(c = getchar())) x = (x << 1) + (x << 3) + (c ^ 48);
	return f ? -x : x;
}

int root,cnt;

void update(int x) {
	node[x].size = (node[x].left ? node[node[x].left].size : 0) + (node[x].right ? node[node[x].right].size : 0) + 1;
}

int merge(int x,int y) {
	if(!x || !y) return x + y;
	if(node[x].rand < node[y].rand) {
		merge(node[x].right,y);
		update(x);
		return x;
	}
	else {
		merge(x, node[y].left);
		update(y);
		return y;
	} 
}

void split(int n,int k,int &x,int &y) {
	if(!n)  x = y = 0;
	else if(k <= node[n].key) {
		split(node[n].left,k,x,y);
		node[n].left = y;
		update(y = n);
	}
	else {
		split(node[n].right,k,x,y);
		node[n].right = x;
		update(x = n);
	}
} 

inline int insert(int k) {
	int x,y;
	split(root,k,x,y);
	node[++ cnt] = Node(k);
	root = merge(merge(x,cnt),y);
}

inline void remove(int k) {
	int x,y,z;
	split(root,k,x,z);
	split(z,k + 1,z,y);
	z = merge(node[z].left,node[z].right);
	root = merge(merge(x,z),y);
}

inline int rank(int k) {
	int x,y;
	split(root,k,x,y);
	if(!x) return 1;
	int r = node[x].size + 1;
	merge(x,y);
	return r;
}

int search(int n,int r) {
	int rank = (node[n].left ? node[node[n].left].size : 0) + 1;
	//rank是节点 n 在以 n 为根节点的子树里的排名。
	if(r == rank) return node[n].key;
	else if(r < n) return search(node[n].left,r);
	else return search(node[n].right,r - rank); 
}

inline int lower(int k) {
	int x,y;
	split(root,k,x,y);
	if(!x) return -0x7fffffff;
	int ans = search(x,node[x].size);
	root = merge(x,y);
	return ans;
}

inline int upper(int k) {
	int x,y;
	split(root,k + 1,x,y);
	if(!y) return 0x7fffffff;
	int ans = search(y,1);
	root = merge(x,y);
	return ans;
}

void dfs(int n) {
	if(node[n].left)dfs(node[n].left);
	printf("%d ", node[n].key);
	if(node[n].right)dfs(node[n].right);
}

int main()
{
    int n = read();

    for(int i = 1, o, x, cnt; i <= n; i ++)
    {
        o = read(); if(o != 7)x = read();
        switch(o)
        {
            case 1 : insert(x); break;
            case 2 : remove(x); break;
            case 3 : printf("%d\n", rank(x)); break;
            case 4 : printf("%d\n", search(root, x)); break;
            case 5 : printf("%d\n", lower(x)); break;
            case 6 : printf("%d\n", upper(x)); break;
            case 7 : dfs(root), puts("");
        }
    }

    return 0;
}
```
