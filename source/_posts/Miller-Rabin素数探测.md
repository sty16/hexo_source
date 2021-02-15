---
title: Miller-Rabin素数探测
date: 2021-02-15 21:30:13
tags: Data Stucture
Categories: Alogrithm
mathjax: true
---

### Miller-Rabin素数探测

费马小定理，如果p为质数，且$gcd(a,p)=1$，那么$a^{p - 1} \equiv 1 (mod\ p)$， 如果p为质数，且a，p互质，那么a的p-1次方除以p的余数等于1。

二次探测定理， 如果p是素数，且x是小于p的正整数，且$x^2 \equiv 1 (mod\ p)$，那么$x = 1$或者$x =p - 1$。

Miller-Rabin素数探测，将p-1(偶数)表示为$d \times 2^r$，其中d为奇数，如果p为素数，那么存在某个$0 \le i < r$使得$a^{d*2^{r - i}} mod\ n = n - 1$，或者$a^d = 1$。

```c++
using ll = long long;
vector<int> prime{2, 3, 5};
ll qpow(ll a, ll b, ll p) {
	ll ans = 1;
	while (b) {
		if (b & 1) ans = ans * a % p;
		a = a * a % p;
		b >>= 1;
	}
	return ans;
}
// 如果是long long会发生溢出，则需要在模运算意义下计算快速积

int TwiceDetect(ll a, ll b, ll p) {
    int r = __builtin_ctz(b);
    ll d = b >> t;
    ll x, y;
    x = y = qpow(a, d, p);
    while (r--) {
        y = x * x % p;
        if (y == 1 && x != 1 && x != p - 1) return 1; //是合数返回1
        x = y;
    }
   	return y != 1;
}


bool miller_rabin(ll p) {
    if (p == 2) return true;
    if (p < 2 || p % 2 == 0) return false;
    for (int i = 0; i < 3; ++i) {
        ll base = prime[i];
        if (p == base) return true;
        if (TwiceDetect(base, p - 1, p)) return 0;
    }
    return 1;
}
```

