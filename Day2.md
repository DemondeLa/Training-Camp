## Blog

### 算法

如果基础不好的录友，建议直接视频讲解，这样避免很多时间浪费，因为没接触过的算法，不是轻易就能靠自己思考想出来的。

拓展题目可以先不做



#### 209 最小长度的数据

**题目建议**： 本题关键在于理解滑动窗口[[1\]](#_msocom_1) ，这个滑动窗口看文字讲解 还挺难理解的，建议大家先看视频讲解。 拓展题目可以先不做。 

题目链接：[**https://leetcode.cn/problems/minimum-size-subarray-sum/**](https://leetcode.cn/problems/minimum-size-subarray-sum/)

文章讲解：[**https://programmercarl.com/0209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.html**](https://programmercarl.com/0209.长度最小的子数组.html)[[2\]](#_msocom_2) [[3\]](#_msocom_3) 

视频讲解：https://www.bilibili.com/video/BV1tZ4y1q7XE



一开始想到的就是使用暴力法，使用双重循环，找到数组中的每个子数组，但是这样做，提交后显示TLE，时间复杂度过高

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        long sum = 0, len = 0, minLen = nums.size() + 1;
        for (long i = 0; i < nums.size(); ++ i) {
            sum = len = 0;
            while (sum < target && (i + len) < nums.size())
                sum += nums[i + len++];
            if (sum >= target && len < minLen)
                minLen = len;
        }
        return minLen == nums.size() + 1 ? 0 : minLen;
    }
};
```

使用滑动窗口法，窗口就是满足条件的最小子序列。

遍历的是起始位置还是终止位置

如果遍历的是起始位置，那么终止位置依然需要遍历所有的位置，这就和暴力法一样了

因此，遍历的是终止位置，而起始位置就是滑动窗口的滑动。

当窗口元素的`sum`满足条件的时候，就开始滑动，即缩小窗口，从而收集不同子数组之和

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        long beg = 0, end;
        long sum = 0, minLen = nums.size() + 1;
        for (end = 0; end < nums.size(); ++ end) {
            sum += nums[end];
            while (sum >= target) {
                if (minLen > end - beg + 1)
                    minLen = end - beg + 1;
                sum -= nums[beg ++];
            }
        }
        return minLen == nums.size() + 1 ? 0 : minLen;
    }
};
```

**PS**简单复习以下八股文，计网中的滑动窗口协议

窗口是发送方或接收方在某一时刻能够发送或接收的数据量；发送方窗口表示在未收到确认的情况下，允许连续发送的最大数据量；接收窗口表示能够接收数据的最大容量，通常和接收方的缓存大小相关



#### 59 螺旋矩阵

**题目建议**： 本题关键还是在转圈的逻辑，在二分搜索中提到的区间定义，在这里又用上了。 [[4\]](#_msocom_4) [[5\]](#_msocom_5) [[6\]](#_msocom_6) [[7\]](#_msocom_7) [[8\]](#_msocom_8) [[9\]](#_msocom_9) 

题目链接：[**https://leetcode.cn/problems/spiral-matrix-ii/**](https://leetcode.cn/problems/spiral-matrix-ii/)

文章讲解：[**https://programmercarl.com/0059.%E8%9E%BA%E6%97%8B%E7%9F%A9%E9%98%B5II.html**](https://programmercarl.com/0059.螺旋矩阵II.html)

视频讲解：https://www.bilibili.com/video/BV1SL4y1N7mV/



在拿到这题的时候，没有思路，所以先看了卡哥的思路

关键：**每画一条边都要坚持一致的左闭右开，或者左开右闭的原则，这样这一圈才能按照统一的规则画下来**

进入循环，首先要确定结束条件，即需要转几圈，这里`n%2`为奇数会剩下中间的位置，为偶数能够正好在`n / 2`圈内转完

每一圈的起始位置是不确定的，每一圈都会变化；同理，终止位置也会随着圈数变化

因此，每一圈转完都需要更新开始位置和结束位置

在每一圈中，先走上边，主要是`j`的变换，从开始到结束，注意结束位置，这里我取的是开区间；再走右边，主要变换的是`i`，也是从开始到结束，结束位置划为下一条边；走下边，从右到左，变化的是`j`，从结束到开始，注意开始位置不走到，最后是左边，同理，由下到上，变换的是`i`

这样就走完了一圈，需要进行更新，开始位置和结束位置，因为走到了小的内圈

走完后，注意判断`n`若为奇数，则还要填最后一个数

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        int start_x = 0, start_y = 0;
        int offset = 1, count = 1;
        int loop = n / 2;
        vector<vector<int>> matrix(n, vector<int>(n, 0));
        while (loop --) {
            int i = start_x, j = start_y;
            for (; j < n - offset; ++ j)
                matrix[i][j] = count ++;
            for (; i < n - offset; ++ i)
                matrix[i][j] = count ++;
            for (; j > start_y; -- j)
                matrix[i][j] = count ++;
            for (; i > start_x; -- i)
                matrix[i][j] = count ++;
            start_x ++, start_y ++;
            offset ++;
        }
        if (n % 2)
            matrix[n / 2][n / 2] = count;
        return matrix;
    }
};
```



#### 区间和

**前缀和是一种思维巧妙很实用** **而且** **很有容易理解的一种算法思想，大家可以体会一下**

##### 文章讲解：[https://www.programmercarl.com/kamacoder/0058.%E5%8C%BA%E9%97%B4%E5%92%8C.html](https://www.programmercarl.com/kamacoder/0058.区间和.html)



首先想到的是使用暴力法，循环找到区间开始位置，然后开始加，代码如下，但是会超时（没错，随想录中“把这个区间的和都累加一遍不就得了，是一道简单不能再简单的题目”就是我）

```cpp
#include <iostream>
#include <vector>

using std::cout;
using std::cin;
using std::vector;

int main()
{
    int n;
    cin >> n;
    vector<int> arr(n);
    for (int i = 0; i < n; ++i)
        cin >> arr[i];
    int a, b;
    while (cin >> a >> b) {
        int sum = 0;
        for (int i = 0; i < n && i <= b; ++i) {
            if (i >= a)
                sum += arr[i];
        }
        cout << sum << "\n";
    }

    return 0;
}
```

使用前缀和的解题技巧：前缀和的思想是==重复利用计算过的子数组之和==，从而降低区间查询需要累加计算的次数。对于设计计算区间和的问题非常有用

看了随想录中的思路后，感觉就是典型的用空间换时间，在输入`a`和`b`前，先遍历一遍数组，并记录到`index`前的元素和到另一个数组中，要区间元素和时，从这个新数组中获取

```cpp
#include <iostream>
#include <vector>

using std::cout;
using std::cin;
using std::vector;

int main()
{
    int n;
    cin >> n;
    vector<int> arr(n), sumArr(n);
    for (int i = 0; i < n; ++i) {
        cin >> arr[i];
        if (i == 0) 
            sumArr[0] = arr[0];
        else sumArr[i] = sumArr[i - 1] + arr[i];
    }

    int a, b;
    while (cin >> a >> b) {
        if (a)
            cout << sumArr[b] - sumArr[a - 1] << "\n";
        else cout << sumArr[b] << "\n";
    }

    return 0;
}
```

以上代码在提交时还是超时，由于在计算 `sumArr` 向量的过程中，`sumArr[i] = sumArr[i - 1] + arr[i]` 这一语句每次需要查找 `sumArr` 和 `arr` 两个不同的 `vector` 的位置;由于 `sum[i]` 和 `arr[i]` 的操作是相对独立的，这种方式对缓存命中率较低，导致计算前缀和时会有一定的性能损失。尤其是在循环体内，这种重复对两个 `vector` 访问的方式，影响了代码执行效率

```cpp
#include <iostream>
#include <vector>

using std::cout;
using std::cin;
using std::vector;

int main()
{
    int n;
    cin >> n;
    vector<int> arr(n), sumArr(n);
    int sum = 0;
    for (int i = 0; i < n; ++i) {
        cin >> arr[i];
        sum += arr[i];
        sumArr[i] = sum;
    }

    int a, b;
    while (cin >> a >> b) {
        if (a)
            cout << sumArr[b] - sumArr[a - 1] << "\n";
        else cout << sumArr[b] << "\n";
    }

    return 0;
}
```

减少了每次循环对两个不同 `vector` 的访问。直接在循环内将 `sum` 累加并赋给 `sumArr[i]`，利用了连续的内存空间存储前缀和值，因此效率更高



#### 开发商购买土地

[https://www.programmercarl.com/kamacoder/0044.%E5%BC%80%E5%8F%91%E5%95%86%E8%B4%AD%E4%B9%B0%E5%9C%9F%E5%9C%B0.html](https://www.programmercarl.com/kamacoder/0044.开发商购买土地.html)



没有思路，直接看随想录的解释，使用前缀和的思想

先获取到每块土地的价值，并求出所有土地的价值总和

再分别求行和列的前缀和

利用前缀和，比如在划分横向的时候，只需从`rows[0]`累加到某行，并用`totalSum - row_diff`来计算另一部分的和

```cpp
#include <math.h>
#include <iostream>
#include <vector>

using std::cout;
using std::cin;
using std::vector;

int main()
{
    int n, m;
    cin >> n >> m;
    int totalSum = 0;// 总土地价值
    vector<vector<int>> ground(n, vector<int>(m));
    for (int i = 0; i < n; ++i) 
        for (int j = 0; j < m; ++j) {
            cin >> ground[i][j];
            totalSum += ground[i][j];
        }
    
    vector<int> rows(n), cols(m);
    // 每一行和列的前缀和
    for (int i = 0; i < n; ++ i)
        for (int j = 0; j < m; ++ j) {
            rows[i] += ground[i][j];
            cols[j] += ground[i][j];
        }

    // 横向划分：计算每一行的前缀和，找到最小差值
    int row_diff = 0, ret = totalSum + 1;
    for (int i = 0; i < n; ++ i) {
        row_diff += rows[i];// 前缀和来获得一行的上半部分的总和
        // 另一部分的总和为totoalSum - row_diff
        ret = std::min(ret, abs((totalSum - row_diff) - row_diff));
        // abs((totalSum - row_diff) - row_diff)表示两部分总和的差值
        // 并更新最小差值
    }
    
    int col_diff = 0;
    for (int j = 0; j < m; ++ j) {
        col_diff += cols[j];
        ret = std::min(ret, abs((totalSum - col_diff) - col_diff));
    }
    
    cout << ret << "\n";

    return 0;
}
```

使用前缀和后，整体时间复杂度会显著降低到 `O(n + m)`，从而可以快速找到最优划分位置

#### 总结：

**题目建议**：希望大家 也做一个自己 对数组专题的总结

文章链接：[**https://programmercarl.com/%E6%95%B0%E7%BB%84%E6%80%BB%E7%BB%93%E7%AF%87.html**](https://programmercarl.com/数组总结篇.html) 



题型有：

- 二分查找
- 双指针法
- 滑动窗口法（主要是窗口的开始位置）
- 模拟
- 前缀和：用于求区间和会非常方便

![数组](https://code-thinking-1253855093.file.myqcloud.com/pics/%E6%95%B0%E7%BB%84%E6%80%BB%E7%BB%93.png)



### 八股文

#### HTTP请求报文和响应报文是怎样的

掌握程度：

●基本结构: 能够描述HTTP请求报文和响应报文的基本结构和各部分的作用。

●关键字段: 对请求行、状态行、请求头和响应头中的常见字段有所了解。

●后续延伸知识点：

○HTTP方法: 能够区分并解释不同的HTTP方法及其用途。

○HTTP 版本：熟悉 HTTP 不同版本的特性和之间的区别。

○状态码: 熟悉常见的HTTP状态码及其含义，特别是2xx、3xx、4xx和5xx系列。

○安全：了解HTTPS、SSL/TLS等安全协议及其在HTTP中的应用。

○优化：知道如何通过请求头和响应头进行性能优化，比如使用ETag、Last-Modified等字段进行 HTTP 缓存控制。

参考学习资料：

●[建议阅读：MDN文档：HTTP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview#基于_http_的系统的组成)

● [技术蛋老师：【HTTP是什么？】](https://www.bilibili.com/video/BV1zb4y127JU/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80)

●[小林coding: HTTP面试题](https://xiaolincoding.com/network/2_http/http_interview.html)

● [基本概念不了解的可以看【HTTP_1.1请求报文格式】](https://www.bilibili.com/video/BV1gg4y1P7Yt/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80)

● [基本概念不了解的可以看【HTTP_1.1响应报文格式】](https://www.bilibili.com/video/BV1Sm4y1J7dr/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80)



##### HTTP请求报文

HTTP请求报文是客户端（通常是浏览器）向服务器发送请求的消息。其结构分为以下几个部分：

- **请求行**

  请求行的格式为：`<请求方法> <请求URL> <HTTP版本>`，通常包含以下三部分：

  1. **请求方法**：表明要对资源进行的操作，常见的有：
     - `GET`：请求获取资源。
     - `POST`：提交数据以处理（如表单提交）。
     - `PUT`：更新资源。
     - `DELETE`：删除资源。
  2. **请求URL**：请求的资源路径，比如`/index.html`。
  3. **HTTP版本**：指定HTTP协议的版本，比如`HTTP/1.1`。

  示例请求行：

  ```bash
  GET /index.html HTTP/1.1
  ```

- **请求头**

  请求头包含若干个键值对，用于提供客户端环境、请求内容等信息。每个请求头都包含字段名和字段值，例如：

  - **Host**：指定目标服务器的域名和端口（常见格式`Host: www.example.com`）。
  - **User-Agent**：说明客户端类型、浏览器信息等。
  - **Accept**：指定客户端可接收的数据类型，如`text/html`。
  - **Accept-Encoding**：表示客户端支持的内容编码，比如`gzip`。
  - **Content-Type**：指示请求体的数据格式，常见的如`application/json`、`application/x-www-form-urlencoded`。
  - **Cookie**：传递客户端的Cookie信息。

  示例请求头：

  ```makefile
  Host: www.example.com
  User-Agent: Mozilla/5.0
  Accept: text/html,application/xhtml+xml
  ```

- **空行**

  在请求头之后，空行用于分隔请求头和请求体。没有内容，仅包含一个换行符。

- **请求体**

  请求体用于传输客户端发送给服务器的数据内容，通常在`POST`、`PUT`等方法中包含请求体。数据格式可能是JSON、XML、表单数据等。

##### HTTP响应报文

HTTP响应报文是服务器对客户端请求的响应。其结构也分为以下几个部分：

- **状态行**

  状态行的格式为：`<HTTP版本> <状态码> <状态描述>`，通常包含以下三部分：

  1. **HTTP版本**：例如`HTTP/1.1`。
  2. **状态码**：3位数字表示请求的处理结果，常见的状态码有：
     - `200`：请求成功。
     - `301`：资源永久移动。
     - `404`：资源未找到。
     - `500`：服务器内部错误。
  3. **状态描述**：对状态码的简短描述，如`OK`、`Not Found`。

  示例状态行：

  ```
  HTTP/1.1 200 OK
  ```

- **响应头**

  响应头也包含若干个键值对，用于提供服务器信息、响应内容属性等。常见的响应头字段包括：

  - **Content-Type**：响应内容的MIME类型，如`text/html`。
  - **Content-Length**：响应体的字节数，便于客户端判断内容长度。
  - **Server**：标识服务器软件信息。
  - **Set-Cookie**：设置客户端的Cookie。
  - **Cache-Control**：缓存控制指令，如`no-cache`、`max-age=3600`。
  - **Location**：用于重定向，指定资源的新的URL。

  示例响应头：

  ```yaml
  Content-Type: text/html
  Content-Length: 342
  Server: Apache
  Cache-Control: no-cache
  ```

- **空行**

  在响应头之后，空行用于分隔响应头和响应体。

- **响应体**

  响应体包含服务器返回给客户端的实际内容，比如HTML页面、JSON数据等。对于静态网页，请求的HTML代码会放在响应体中；对于API请求的响应，JSON或XML数据一般也是在此处返回。

| 报文部分   | HTTP请求报文            | HTTP响应报文                   |
| ---------- | ----------------------- | ------------------------------ |
| **起始行** | 请求行：方法、URL、版本 | 状态行：版本、状态码、状态描述 |
| **头部**   | 请求头：请求的相关信息  | 响应头：响应的相关信息         |
| **空行**   | 分隔头部和主体          | 分隔头部和主体                 |
| **主体**   | 请求体：请求的实际数据  | 响应体：服务器的实际返回内容   |



#### HTTP请求方式有哪些

掌握程度：

●方法定义: 能够清晰地定义每个请求方法的基本用途，知道哪些方法可能会改变服务器状态，哪些不会，并能够根据场景选择合适的HTTP方法。

●安全性: 了解每种方法在安全性方面的考虑，特别是GET和POST方法的区别。

●幂等性: 理解幂等性的概念，并能识别哪些HTTP方法是幂等的（如GET、PUT、DELETE）。

参考资料：

[MDN文档：HTTP请求方法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods)

[菜鸟教程：HTTP请求方法](https://www.runoob.com/http/http-methods.html)



##### GET方法

- 用途：用于从服务器请求获取指定资源的内容，常用于获取页面、文件等数据。
- 是否改变服务器状态：不会改变服务器的状态，是一种安全的请求。
- 幂等性：是幂等的。无论发送多少次GET请求，服务器的状态不会改变（如请求一个页面，页面内容不会因多次请求而变化）。
- 适用场景：适用于获取资源或数据，不会影响服务器的状态。例如访问网页、获取API数据等。

##### POST方法

- 用途：用于向服务器提交数据并处理，比如表单提交、创建资源等。
- 是否改变服务器状态：会改变服务器状态。POST请求会创建新的资源或触发服务器进行处理。
- 幂等性：不是幂等的。多次提交相同的POST请求可能会创建多个相同的资源或导致多次操作（例如订单创建、留言提交等）。
- 适用场景：适用于创建新资源或提交表单。需要服务器处理或创建资源的情况通常使用POST。

##### PUT方法

- 用途：用于上传或更新服务器上的指定资源（如上传文件、更新某个数据库条目）。
- 是否改变服务器状态：会改变服务器状态。PUT请求会创建或替换资源。
- 幂等性：是幂等的。多次对同一资源发出相同的PUT请求，服务器最终的状态相同（例如更新用户信息，发送多次请求后，用户信息依然与最后一次请求相同）。
- 适用场景：适用于更新资源。用于客户端知道资源的URL并需要替换资源内容的情况。

##### DELETE方法

- 用途：请求删除服务器上的指定资源（如删除文件、删除数据库记录）。
- 是否改变服务器状态：会改变服务器状态。DELETE请求会删除资源。
- 幂等性：是幂等的。多次对同一资源发出DELETE请求，最终的结果相同（资源已被删除）。
- 适用场景：用于删除资源。当客户端需要请求服务器删除指定资源时，使用DELETE。

##### PATCH方法

- 用途：用于对资源进行部分更新。与PUT不同的是，PATCH只修改部分字段而非整个资源。
- 是否改变服务器状态：会改变服务器状态。PATCH请求会修改资源的部分内容。
- 幂等性：通常是幂等的，但实际情况依赖于具体实现。如果多次发出相同的PATCH请求，对资源的更改结果应该是相同的。
- 适用场景：用于部分更新资源，适合仅更改资源中部分字段的情况。

##### HEAD方法

- 用途：请求资源的头部信息（如响应头），但不返回资源本身的数据。通常用于获取资源的元信息，如大小、类型、上次修改时间等。
- 是否改变服务器状态：不会改变服务器状态，是一种安全的请求。
- 幂等性：是幂等的。多次发出HEAD请求不会改变资源或服务器的状态。
- 适用场景：用于获取资源的元信息。适合获取资源存在性、大小、修改时间等元信息。

##### OPTIONS方法

- 用途：用于查询服务器支持的请求方法，通常用于检查CORS（跨域资源共享）策略或查看服务器支持哪些HTTP方法。
- 是否改变服务器状态：不会改变服务器状态，是一种安全的请求。
- 幂等性：是幂等的。多次发出OPTIONS请求不会影响服务器的状态。
- 适用场景：用于查询服务器支持的请求方法，常见于处理跨域请求。

##### TRACE方法

- 用途：用于诊断或调试，服务器将收到的请求原封不动地返回给客户端。TRACE请求可帮助排查中间代理或传输的错误。
- 是否改变服务器状态：不会改变服务器状态，是一种安全的请求。
- 幂等性：是幂等的。多次发出TRACE请求不会影响服务器的状态。
- 适用场景：适用于调试或测试网络路径，帮助排查传输过程中发生的问题。

**幂等性**是指多次对服务器执行相同的操作，结果和执行一次的效果相同。幂等性通常是衡量HTTP方法安全性的一个重要方面。



#### GET请求和POST请求的区别

掌握程度：

●能够清晰地描述GET和POST请求的基本区别和各自的用途，能够根据场景选择合适的请求方法。

●知道GET请求数据附加在URL中，POST请求数据在请求体中。

●理解GET和POST请求在安全性和幂等性方面的差异。

参考资料：

[小林coding: GET请求和POST请求的区别](https://xiaolincoding.com/network/2_http/http_interview.html#get-和-post-有什么区别)

[MDN文档：POST方法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)

[MDN文档：GET方法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)



| 特性         | GET请求                                   | POST请求                                        |
| ------------ | ----------------------------------------- | ----------------------------------------------- |
| **用途**     | 请求获取服务器的资源或数据                | 向服务器发送数据，通常用于创建或提交            |
| **参数位置** | 参数通过URL传递（一般在URL末尾的`?`之后） | 参数在请求体中传递                              |
| **可见性**   | URL参数明文显示（不适合传递敏感信息）     | 数据放在请求体中，用户不可见                    |
| **数据大小** | 受URL长度限制（一般不超过2KB）            | 请求体传输数据，大小不受URL限制，理论上没有限制 |
| **缓存**     | 可被浏览器缓存                            | 默认不会被缓存                                  |

GET用于获取资源数据或请求服务器信息，通常适用于：

- **页面访问**：请求网页资源，如访问`https://www.example.com`。
- **静态资源**：获取图片、样式表、脚本等静态资源。
- **搜索查询**：将查询参数放在URL中，如搜索引擎的关键词查询。
- **API数据获取**：请求API接口数据（如天气数据）。

POST用于提交数据到服务器，通常适用于：

- **表单提交**：提交包含个人信息的注册表单、登录表单等。
- **文件上传**：将图片、视频等文件数据上传到服务器。
- **数据提交**：比如评论、留言、反馈等场景的数据提交。
- **创建新资源**：在REST API中，POST通常用于创建新资源（如新用户、新订单）。

安全性差异

- **GET**：GET请求的数据通过URL传递，**明文显示**，容易被缓存和暴露在浏览器历史记录、书签、代理服务器中，不适合传输敏感信息（如密码、个人信息等）。
- **POST**：POST请求的数据在请求体中传递，虽然不是加密传输，但至少不会直接暴露在URL中，相对安全，适合传输较为敏感的数据。

**注意**：GET和POST方法本身都没有加密传输数据的功能。如果需要保护数据传输安全，通常会使用HTTPS协议。

幂等性差异

- **GET的幂等性**：GET请求是**幂等**的，即多次请求不会改变服务器状态。无论请求多少次同一个资源，服务器的状态不会发生变化，用户只是获取数据而已。
- **POST的幂等性**：POST请求**非幂等**，即多次相同的POST请求可能会导致服务器状态发生变化。例如，重复提交表单可能会多次创建相同的资源（如重复下订单）。



### 总结

感觉今天的题目都比较难，一开始都是没有思路（或者是暴力法），随想录中的一些思路都非常好，虽然在看了卡哥的思路之后能够写出来，但是还需要多练习，另外感觉前缀和的思路掌握的还不是很好，抽空还需要多练习一下卡码网的两道题目
