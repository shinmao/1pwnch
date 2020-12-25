---
title: "Roblox OA 2020"
date: 2020-12-24T17:47:27-05:00
draft: false
categories:
- Algorithm
tags:
- OA準備
---

Roblox是我2020收到的最後一份OA，真的真的收到OA就已經非常開心了。準備了幾題考古題然後興奮得打開OA，roblox的OA一樣是host在hackerrank上的，大概三題90分鐘。題目的難度比起之前抖音我覺得友善許多，不過還是沒寫完 😥... 寫到第三題的一半實在是沒想法只好投降。在這篇文章中想分享一下準備的過程，網路上有很多人在討論考古題，思維有些是有問題或code也沒有寫出來，我想在這裡做個總結，也會附上all-pass的code (至少我自己會測試5-10個左右的test cases)。如果覺得有錯的話歡迎留言討論 🤙

## Word Compression
![](/12-24-20/word-compression.png)
題目要求不斷刪除字串中k個連續相同的字元。以題目給的Example `abbcccb`, `k = 3` 為例，先刪掉3個`c`，所以會變成`abbb`，再刪掉3個`b`，最後會變成`a`。這題很容易聯想到`stack`資料結構的應用，怎麼想的呢？我們由左到右讀取字串時，當push的字元跟stack頂的字元相同且**連續count**累積到3時，就把這個字給pop出來。想要紀錄各單字的count的話，大家都會想用`map`吧，這樣就可以用`O(1)`的時間複雜度檢查出現次數是否為k。但如果同一字母出現了好幾組呢？e.g. `accbbcccbc`。這組字串跟上面的例子一樣最後都會變成`a`，但是`ccc`有兩組：第一組`ccc`作為`bbb`被刪除之後拼接起來，**連續count**才會累積到3;而第二組`ccc`作爲第一組被刪除的對象，一開始**連續count**就是3了。如何？如果map用字母作為key去查詢連續count的話肯定會很麻煩唄！  

能夠區別這兩組c的方式就是把他們宣告成object，object因記憶體位置不同而被視為相異。以下為思路：  
1. 宣告一個結構體，裡面同時儲存單字和他目前**位置**累積的連續count
2. stack儲存的對象就是這個結構體，讀取字串的同時，把字母轉換為結構體的type存入stack  
3. 若是stack頂的字母連續count為k，則pop這組字母  
4. 若是stack頂的字母跟當前讀入的字母一樣，則為連續count+1  
5. 若不一樣，則開一個新的結構體，連續count為1
6. 有時候是我們讀完整個字串，剩下的字母拼接在一起才變成一組字母，所以我們最後還要再加判斷一次 (e.g. `ccbbcccbc`)
7. 最後把stack裡剩下的內容倒著輸出，Done

以下為`C++`版本對應的code：  
```cpp
#include <bits/stdc++.h>
#define _ ios::sync_with_stdio(0);cin.tie(0);
using namespace std;
// variable k and str
// variable represents number of consecutive characters to be removed from str

struct Freq {  // 1.
    char c;
    int f;
};

int main() { _
    int k = 0;
    string str = "";
    cin >> k >> str;
    stack<Freq> st;
    for(auto i = 0; i < str.size(); ++i) {    // 2.
        char c = str[i];
        // if stack is not empty and top char has frequency k
        if(st.size() > 0 && st.top().f == k) {     // 3.
            char curc = st.top().c;
            while(st.size() > 0 && st.top().c == curc)
                st.pop();
        }

        // if stack is not empty and top char is equal to current char
        if(st.size() > 0 && st.top().c == c) {    // 4.
            struct Freq new_c;
            new_c.c = c;
            new_c.f = st.top().f + 1;
            st.push(new_c);
        }else {      // 5.
            struct Freq new_c;
            new_c.c = c;
            new_c.f = 1;
            st.push(new_c);
        }
    }

    if(st.size() > 0 && st.top().f == k) {     // 6.
        char curc = st.top().c;
        while(st.size() > 0 && st.top().c == curc)
            st.pop();
    }

    string res;
    while(st.size() > 0) {
        char s = st.top().c;
        res += s;
        st.pop();
    }

    reverse(res.begin(), res.end());     // 7.
    cout << res << '\n';

    return 0;
}
```

## Smart Sale
![](/12-24-20/smart-sale.png)
有一個銷售員要出售它的產品，產品的種類都賦予一個id(也就是同一個id的產品可能會有多個)。現在老闆指定一個出售數量，讓銷售員在出售完這個數量的產品後，剩下的種類最少。這題很簡單，銷售策略就是從數量最少的產品賣起！因此我們需要每一種產品的數量，並且排序。紀錄產品數量的方法就用`map`，而為了排序，我選擇用一個`vector`去存產品的資訊。以下為這個思路的code:  
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> arr = {2, 4, 1, 5, 3, 5, 1, 3};
    int n = arr.size();
    int mi = 2;

    unordered_map<int, int> m;
    // v<count, integer>
    vector<pair<int, int> > v;
    int count = 0;

    for(int i = 0; i < n; i++) {
        m[arr[i]]++;
    }

    for(auto it = m.begin(); it != m.end(); it++) {
        v.push_back(make_pair(it->second, it->first));
    }

    sort(v.begin(), v.end());

    // remove starting from the element with lowest count
    int size = v.size();

    for(int i = 0; i < size; i++) {
        if(v[i].first <= mi) {
            mi -= v[i].first;
            count++;
        }else{
            cout << size - count;
            return 0;
        }
    }

    cout << size - count;

    return 0;
}
```
`vector`裏存的是一組一組的`<count, id>`，因為`vector`預設會用first排序，因此能讓產品依照他們的count由下排到大。剩下的部分都很好理解 😉

## Lifting Weights
![](/12-24-20/lifting-weights.png)
如果你了解什麼是背包問題，那這題就解決了 - 相當於沒有物品價值的背包問題。背包問題是DP中一種常見的題型，這邊我還是會從頭到尾介紹一次思路，這種思路可以作為DP的模板，也可以用同樣的思路解決所有的背包問題！  

先看狀態和選擇，狀態有**還可以舉多重的啞鈴(capacity)**和**可選擇的啞鈴**，選擇就是**舉/不舉這個啞鈴**。為了方便狀態轉移，我如下定義`dp[i][j]: 前i個啞鈴在Ollie只可以再舉j的重量時 所能舉的最大重量`。  

根據**舉/不舉這個啞鈴**的選擇，狀態轉移方程如下：
```cpp
// 以舉ith啞鈴為前提的話，為了確保Ollie可以舉ith啞鈴，我們讓cap預留ith啞鈴的重量
// dp[i - 1][j - A[i]] 在這個限制下，之前能舉的最大重量
dp[i][j] = dp[i - 1][j - A[i]] + A[i]
// 不舉的話就代表能舉的最大重量一樣
dp[i][j] = dp[i - 1][j]
```
搞定！轉換成code吧：  
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> weights = {1, 3, 5};
    int maxcap = 7;

    // condition: available cap and items
    // choice: put or not put
    // dp[i][lb]: max lb with first i items and lb size
    // if not put:
    // dp[i][lb] = dp[i - 1][lb]
    // if put:
    // dp[i][lb] = dp[i - 1][lb - weights[i - 1]] + weights
    int n = weights.size();
    vector<vector<int>> dp(n + 1, vector<int>(maxcap + 1, 0));

    for(int i = 0; i <= n; i++) {
        // if lb == 0
        dp[i][0] = 0;
    }

    for(int j = 0; j <= maxcap; j++) {
        // if no items in bag
        dp[0][j] = 0;
    }

    for(int i = 1; i <= n; i++) {
        for(int lb = 1; lb <= maxcap; lb++) {
            if(lb >= weights[i - 1])
                dp[i][lb] = max(dp[i - 1][lb],
                        dp[i - 1][lb - weights[i - 1]] + weights[i - 1]);
            else
                dp[i][lb] = dp[i - 1][lb];
        }
    }

    cout << "Max size you can fill with: " << dp[n][maxcap];

    return 0;
}
```  
狀態轉移方程中還要確保會不會out of bound喔！  
我之後也打算花一個篇章介紹背包問題的系列，敬請期待 😉

## Build Offices
![](/12-24-20/build-offices.png)
這題當時哀鴻遍野。題目要求在地圖上找出offices到周邊建築物的最短路徑並求出該路徑中的最大值，But**題目並沒有給地圖**！最短路徑好說，肯定跟BFS脫不了關係，但如何選出offices的最佳位置呢？暴力破解？別傻了 🤬 TLE你能負責嗎？  

不過，這題的確就讓我們用暴力破解，以後多注意注意題目的限制條件：`w * h <= 28`。這意味著整個地圖頂多就28個位置讓我選而已，假設題目要求我建造五個offices，那麼暴力破解最差也只需要98,280次的運算，每次再乘上BFS線性的時間複雜度，是不至於會TLE的！  

實際上，我也有實現比較好的offices放置策略，其實畫個地圖就很好懂：把offices放得均勻一點就好。先把地圖拉長成一維，e.g. `4*7`的地圖我把它拉長成`1*28`。若要建造五個offices，我會在每`28 / 5 = 5`的距離中就放置一個office。若想把它轉換成二維的座標也很容易，我把地圖變回`4*7`的case，一維的idx 9除以地圖的寬度即可：`[9/5][9%5]`。  

上面我的策略offices的放置點也有好幾種可能，所有的可能我就用DFS來列舉，每一次列舉都會call一次BFS來算最短路徑中的最大值，並且比較前幾次的紀錄來獲得最後的答案。到這裡我覺得這題真的出得很棒，同時讓我能夠重新複習的DFS和BFS。DFS中我用了backtracking，BFS中要注意一定要分層遍歷我們才能算出路徑長度！  

現在就讓我把上面的思路轉換成code：  
```cpp
#include <bits/stdc++.h>
using namespace std;

int w, h, n;
vector<int> dirx = {0, 0, 1, -1};
vector<int> diry = {1, -1, 0, 0};
int maxdistance = INT_MAX;

// help convert (x, y) to decimal-based position
int calc(int x, int y) {
    return (w * x) + y;
}

// check whether out of bound and visited before in bfs
bool check_valid(int x, int y, unordered_set<int> visited) {
    if(x < 0 || x >= h)
        return false;
    if(y < 0 || y >= w)
        return false;
    if(visited.count(calc(x, y)))
        return false;
    return true;
}

// level-ordered bfs and each time find the longest path on map
int bfs(vector<vector<int>> map,
        vector<pair<int, int>> start_point) {
    int local_max = 0;
    queue<pair<int, int>> q;
    unordered_set<int> visited;

    for(auto s : start_point) {
        q.push(s);
        visited.insert(calc(s.first, s.second));
    }

    int dist = 0;
    while(!q.empty()) {
        int sz = q.size();
        dist++;
        for(int i = 0; i < sz; i++) {
            pair<int, int> head = q.front();
            q.pop();
            for(int i = 0; i < 4; i++) {
                pair<int, int> neighbor = make_pair(head.first + dirx[i], head.second + diry[i]);
                if(check_valid(neighbor.first, neighbor.second, visited)) {
                    map[neighbor.first][neighbor.second] = dist;
                    visited.insert(calc(neighbor.first, neighbor.second));
                    q.push(neighbor);
                }
            }
        }
    }

    // after visiting the whole map
    // find the maxdistance
    for(int i = 0; i < h; i++) {
        for(int j = 0; j < w; j++) {
            if(map[i][j] > local_max)
                local_max = map[i][j];
        }
    }

    return local_max;
}

// list all the cases for starting point: 0
// in fact, not all the cases
// I considered optimal start point would show up in each distance of (w*h/n)
void dfs(vector<vector<int>> map,
        int d,
        int n_,
        int n, 
        vector<pair<int, int>> start_point) {
    if(n_ == n) {
        // cout << "Map: " << '\n';
        /**
        for(int i = 0; i < map.size(); i++) {
            for(int j = 0; j < map[i].size(); j++) {
                cout << map[i][j] << ' ';
            }
            cout << '\n';
        }
        cout << "distance?: ";**/
        if(bfs(map, start_point) < maxdistance) {
            maxdistance = bfs(map, start_point);
            cout << maxdistance << '\n';
        }
        return;
    }

    for(int i = 0; i < d; ++i) {
        int pos = n_ * d + i;
        map[pos / w][pos % w] = 0;
        start_point[n_].first = pos / w;
        start_point[n_].second = pos % w;
        dfs(map, d, n_ + 1, n, start_point);
        map[pos / w][pos % w] = INT_MAX;
        start_point[n_].first = -1;
        start_point[n_].second = -1;
    }
}

int main() {
    cin >> w >> h >> n;
    int d = (w*h)/n;   // build one office in each d distance
    vector<vector<int>> map(h, vector<int>(w, INT_MAX));
    vector<pair<int, int>> start_point(n, make_pair(-1, -1));
    dfs(map, d, 0, n, start_point);
    cout << "Max distance of one of optimal solution is: " << maxdistance << '\n';

    return 0;
}

```
BFS和DFS的演算法細節我這裡就不詳述了...  

更多的Test cases有人紀錄在Google Doc裏，附上個[連結](https://docs.google.com/document/d/1rcbfqoJW_7e5hAAdA80qx7S-Z3JTgSqjfr_PPPJcj-A/edit)。

## Ancestral Names
![](/12-24-20/ancestral-names.png)
這題會給幾個名字，每個名字的樣子都是(英文名字 + 羅馬數字)。題目要求我們把這幾個名字排序，排序優先看英文名字的部分，按照ASCII小到大，前綴相同的話，長的排比較後面。若英文名字一樣，則看羅馬數字的部分，要比較羅馬數字的話我要自己把他換成integer來做比較(LC上面有比較簡單的版本 - Roman to int)。  

轉換完之後sort可能挺簡單，但是題目要求輸出轉換前的對應版本，因此用個map紀錄比較好。在map裏，我打算key就是原始的名字，而value則是名字和羅馬數字轉換後的integer。再來就是這題的重頭戲了。`C++`中，map預設是基於key作排序，那如何用value作排序的？**經典做法是創造一個vector來儲存map的內容，並且重寫排序方法**。先看一下我附上的code:  
```cpp
#include <bits/stdc++.h>
using namespace std;

int conDecimal(char c) {
    switch(c) {
        case 'I':
            return 1;
        case 'V':
            return 5;
        case 'X':
            return 10;
        case 'L':
            return 50;
        default:
            return 1;
    }
}

int romanInt(string s) {
    int res = conDecimal(s[0]);
    for(int i = 1; i < s.size(); i++) {
        res += conDecimal(s[i]);
        if(conDecimal(s[i - 1]) < conDecimal(s[i]))
            res -= 2 * conDecimal(s[i - 1]);
    }

    return res;
}

bool cmp(pair<string, pair<string, int>> a, pair<string, pair<string, int>> b) {
    pair<string, int> resa = a.second;
    pair<string, int> resb = b.second;
    if(resa.first == resb.first)
        return resa.second < resb.second;
    // strcmp can only be used on char array
    // compare is better choice on string
    //
    // the order you want should return true
    return resa.first.compare(resb.first) < 0;
}

int main() {
    vector<string> names = {"Steven XL", "Steven XVI", "David IX", "Mary XV", "Mary XIII", "Mary XX"};
    map<string, pair<string, int>> name_map;

    for(auto name : names) {
        for(int i = 0; i < name.size(); i++) {
            if(name[i] == ' ') {
                name_map[name] = make_pair(name.substr(0, i), romanInt(name.substr(i + 1)));
            }
        }
    }

    vector<pair<string, pair<string, int>>> sorted_names;
    for(auto it : name_map) {
        sorted_names.push_back(make_pair(it.first, it.second));
    }

    sort(sorted_names.begin(), sorted_names.end(), cmp);

    for(auto it : sorted_names) {
        cout << it.first << '\n';
    }

    return 0;
}
```
重寫排序方法的部分在`cmp()`，因為我自己也常常忘記怎麼寫，所以這裡詳細紀錄一下思路：  
```cpp
// return type 為 boolean
// parameter data type 就是 element 的 data type
bool cmp(a, b) {
    return a < b;
}
// 上面意味著回傳 (a - b < 0) 是否為真
// 前面的 a 減後面的 b 小於 0 為真代表這是ascending order
// 因此如果要descending order，則return a > b
```  
另外一個值得注意的點是字串與字串的比較：`C++`中耳熟能詳的是`strcmp()`，但這是針對`char`的。若是要針對`string`的，用`compare`會方便許多！  

這題的細節大概就這麼多了，建議自己寫一次sorting function喔！

## Largest Sub-Grid
![](/12-24-20/largest-sub-grid.png)
題意很容易理解，square array中我們找出總和小於或等於maxSum的最大型sub-grid，並且回傳邊長。看到子陣列，又需要我們算總和，很容易就聯想到**prefix sum**！二維前綴和怎麼求算是基礎知識，我這邊就不再詳述，重點是如何找出符合條件的sub-grid？  

sub-grid的邊長和對應的總和是有**單調性**的！單調在哪呢？邊長變長，那麼整體的總和一定變大，因此我可以用binary search來解。下面是我實現的code:  
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<vector<int>> grid = {{1, 1, 1, 1}, {2, 2, 2, 2}, {3, 3, 3, 3}, {4, 4, 4, 4}};
    int maxSum = 39;

    int n = grid.size();
    vector<vector<int>> prefix_sum(n, vector<int>(n, 0));
    int maxgrid = 0;
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++) {
            if(i == 0 && j == 0) {
                prefix_sum[i][j] = grid[i][j];
            }else if(i == 0) {
                prefix_sum[i][j] = prefix_sum[i][j - 1] + grid[i][j];
            }else if(j == 0) {
                prefix_sum[i][j] = prefix_sum[i - 1][j] + grid[i][j];
            }else{
                prefix_sum[i][j] = prefix_sum[i - 1][j] + prefix_sum[i][j - 1] + grid[i][j] - prefix_sum[i - 1][j - 1];
            }
            maxgrid = max(maxgrid, grid[i][j]);
        }
    }
    if(maxSum < maxgrid)
        cout << '0' << '\n';
    if(maxSum >= maxgrid)
        cout << n << '\n';
    cout << "Done!" << "\n";
    // binary search
    int l = 0, r = n;
    while(l <= r) {
        int m = l + (r - l)/2;
        int sum = 0;
        // check all the subgrid with width of m
        // x, y represents the right bound of subgrid
        if(m - 1 < 0 || m - 1 >= n) {
            cout << '0' << '\n';
            return 0;
        }
        for(int x = m - 1; x < n; x++) {
            for(int y = m - 1; y < n; y++) {
                cout << "(l, r, x, y, m): " << l << ' ' << r << ' ' << x << ' ' << y << ' ' << m << ' ';
                int total = prefix_sum[x][y];
                if(x >= m) {
                    total -= prefix_sum[x - m][y];
                }
                if(y >= m) {
                    total -= prefix_sum[x][y - m];
                }
                if(x >= m && y >= m) {
                    total += prefix_sum[x - m][y - m];
                }
                cout << total << '\n';
                sum = max(sum, total);
        }
        if(sum > maxSum) {
            r = m - 1;
        }else if(sum < maxSum) {
            l = m + 1;
        }else if(sum == maxSum) {
            l = m + 1;
        }
    }
    if(r < 0) {
        cout << '0' << '\n';
        return 0;
    }
    return 0;
}
```
這裡的binary search要找出右側的邊界，因此就算總和等於`maxSum`，我還是要繼續把搜索空間往右逼。不過這份code我其實還沒完成，binary search中如何列舉邊長為`m`的sub-grid。LC中有人是直接找邊長為`m`的並且總和最大的sub-grid來比較，不過這不就失去了邊長同樣為`m`但是總和較小的case嘛？上面這份code能夠pass題目給sample cases，但是卻無法pass我自己給的test case: `[[2, 4, 7], [5, 6, 8], [3, 7, 9]]`。上面這份code會回傳`1`，但其實`[[2, 4], [5, 6]]`便可以小於20了...

## Conclusion
一些easy level的題目我就沒有整理在這了 e.g. lowest key pressed。Largest sub-grid的部分目前還沒有想法怎麼處理自己的test case 😰 期待也需要準備OA的戰友們能從這篇文章中獲得些什麼，若思維或者code有出錯的地方歡迎留言，我會定期回覆！