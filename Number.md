## Number

### Ugly Number

维护三个指针，分别指向*2 *3 *5的最小值在哪里。

```c++
int GetUglyNumber_Solution(int index) {
        vector<int> result;
        result.push_back(1);
        int p2 = 0, p3 = 0, p5 = 0;
        for(int i = 0;i < index;i++){
            int newNum = min(result[p2] * 2, min(result[p3] * 3, result[p5] * 5));
            result.push_back(newNum);
            if(result[result.size() - 1] == result[p2] * 2) p2++;
            if(result[result.size() - 1] == result[p3] * 3) p3++;
            if(result[result.size() - 1] == result[p5] * 5) p5++;
            //为了防止出现重复的，所以必须每个if都走一遍，这样如果有重复，比如6(2*3 3*2)，则这个时候两个指针都往前走。精妙！
        }
        return result[index - 1];
    }
```

### Cyclic section

找出循环节

```c++
string GetLoop(int a, int b) {
	vector<int> div;
	vector<int> mod;
	int sign = 0; // 1 represents limited, 2 represents unlimits
	int begin = 0, end = 0;

	if (a % b == 0) return to_string(a / b);
	div.push_back(a / b);
	mod.push_back(a % b);

	for (int i = 1; sign == 0; i++) {
		int currentDiv = (mod[i - 1] * 10) / b;
		int currentMod = (mod[i - 1] * 10) % b;
		div.push_back(currentDiv);
		mod.push_back(currentMod);

		if (mod[i] == 0) {
			sign = 1;
			break;
		}
		for (int j = 0; j < i; j++) {
			if (mod[j] == mod[i]) {
				sign = 2;
				begin = j;
				end = i;
				break;
			}
		}
	}
	string ret = "";

	for (int i = 0; i <= end; i++) {
		ret += to_string(div[i]);
		if (i == 0 && end > 0) ret += ".";
		if (sign == 2 && i == begin) ret += "(";
	}
	if (sign == 2) ret += ")";

	return ret;
}
```

