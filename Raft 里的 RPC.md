

## Protobuf

使用

1. 编写 .proc

   1. 定义 message

      1. string, bytes, int32, bool, map
      2. reperted 用来定义数组
      3. 可以嵌套 message

   2. 设置对象

      1. A.set
      2. 设置嵌套messgae：
         1. auto p* = A.**mutable_**var..()
            1. p->set_... 
      3. 设置 list
         1. auto p1* = A.add_var...();
         2. p1->set...

   3. 设置 service 和 method

      1. ```c++
         option cc_generic_services = true
         
         service Myservice
         {
             rpc func1(request1) return(responce1);
             rpc func2(request2) return(responce2);
         }
         ```

      2. ```c++
         // 生成 Myservice 类
         
         class Myservice : public google::protobuf::Service {
           ...
           // 定义的 rpc 方法
           virtual void func1(
           	::Pro.....::RpcController *contorller,
             const Request* resquest,
            	Resonse* response,
             Closure* done, 
           );
               
           // 服务描述符
           // 服务名，方法名
           const ::...::ServiceDescriptor* GetDescriptor();
           ...
         };
         
         ```

      3. ```c++ 
         class Myservice_stub : public Myservice{
           Myservice_stub(RpcChannel *channel);
           virtual void func1(
           	::Pro.....::RpcController *contorller,
             const Request* resquest,
            	Resonse* response,
             Closure* done, 
           ) {
           	channel->call_method(descriptor->method(0), controller, request, esponse, done);
           }
         };
         
         
         // RpcChannl 是一个抽象类，有一个 call_method 纯虚函数 
         ```

      4. ```c++
         // 定义 MyRpcChannnel
         class MyRpcChannel : public RpcChannel {
             void call_method(...) {
                 ...
             }
         } 
         ```

      5. 

      6. 





发布服务

* NotifyService 发布服务
  * 获取服务描述信息 service->GetDescriptor()
  * 获取服务名 name()
  * 获取服务数量 method_count()
  * 获取服务名，服务描述符
  * 插入 unordered_map



协议

```c++
header_size | RpcHeader | ARGS
 |-->4 bytes  |--> message   |--> message
```









// 华为机试

#include <iostream>
#include <algorithm>
#include <string>
#include <stack>
// we have defined the necessary header files here for this problem.
// If additional header files are needed in your program, please import here.

bool is_digit(char & ch) {
    if (ch >= '0' && ch <= '9') {
        return true;
    }
    return false;
}

bool is_alpha(char & ch) {
    if (ch >= 'a' && ch <= 'z' || ch >= 'A' && ch <= 'Z') {
        return true;
    }
    return false;
}

int main()
{
    string pattern;
    string str;
    string ans = "!";
    cin >> pattern >> str;
    
    stack<int> int_stk;
    stack<string> str_stk;
    int times = 0;
    string cur;
    for (auto e : pattern) {
        if (is_digit(e)) {
            int digit = e - '0';
            times = times * 10 + digit;
        }
        else if (e == 'N') {
            cur += 'N';
        }
        else if (e == 'A') {
            cur += 'A';
        }
        else if (e == '(') {
            int_stk.push(times);
            str_stk.push(cur);
            times = 0;
            cur = "";
        }
        else if (e == ')'){
            string pre = str_stk.top();
            str_stk.pop();
            int t = int_stk.top();
            int_stk.pop();
            for (int i = 0; i < t; i++) {
                pre += cur;
            }
            cur = pre;
        }
    }
    
    pattern = cur;
    // cout << pattern << endl;
    
    string temp = str;
    
    for (auto &e : temp) {
        if (is_digit(e)) {
            e = 'N';
        }
        else if (is_alpha(e)) {
            e = 'A';
        }
    }
    
    // cout << "temp " << temp << endl;
    
    int pos = temp.find(pattern);
    
    if (pos != string::npos) {
        ans = str.substr(pos, pattern.size());
    }
    
    cout << ans;
    // please define the C++14 input here. For example: int a,b; cin>>a>>b;;
    // please finish the function body here.
    // please define the C++14 output here. For example:cout<<____<<endl;
    
    return 0;
}

#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>
using namespace std;
// we have defined the necessary header files here for this problem.
// If additional header files are needed in your program, please import here.


void print(vector<int> &ans) {
    for (int i = 0; i < ans.size(); i++) {
        cout << ans[i];
        if (i != ans.size() - 1) {
            cout << " ";
        }
    }
    cout << endl;
}
int main()
{
    int target;
    int n;
    cin >> target >> n;
    vector<int> nums(n);
    for (int i = 0; i < n; i++) {
        cin >> nums[i];       
    }
    
    int sum = 0;
    for (auto e : nums) {
        sum += e;
    }
    if (n <= 1 || sum != 2 * target) {
        cout << -1 << endl;
        return 0;
    }


​    
​    vector<vector<bool>> dp(n+1, vector<bool>(target+1, false));
​    for (int i = 0; i < n+1; i++) {
​        dp[i][0] = true;
​    }
​    
​    for (int i = 1; i <= n; i++) {
​        for (int j = 0; j <= target; j++) {
​            dp[i][j] = dp[i-1][j];
​            if (j - nums[i-1] >= 0) {
​                if (dp[i-1][j - nums[i-1]]) {
​                    dp[i][j] = (dp[i][j] || dp[i-1][j - nums[i-1]]);
​                }
​            }
​        }
​    }
​    // cout << dp[n][target] << endl;


​    
​    int x = n, t = target;
​    vector<int> ans1;
​    vector<int> ans2;
​    
​    if (dp[n][target]) {
​        while (x >= 1) {
​            if (dp[x-1][t]) {
​                ans1.push_back(nums[x-1]);
​                x--;
​                continue;
​            }
​            if (dp[x][t - nums[x-1]]) {
​                // cout << nums[x-1] << endl;
​                ans2.push_back(nums[x-1]);
​                t -= nums[x-1];
​                x--;
​            }
​        }
​    }
​    sort(ans1.begin(), ans1.end());
​    sort(ans2.begin(), ans2.end());
​    
​    if (ans1[0] < ans2[0] || ans1[0] == ans2[0] && ans1.size() > ans2.size()) {
​        print(ans1);
​        print(ans2);
​    }
​    else {
​        print(ans2);
​        print(ans1);
​    }
​    
    // please define the C++14 input here. For example: int a,b; cin>>a>>b;;
    // please finish the function body here.
    // please define the C++14 output here. For example:cout<<____<<endl;
    
    return 0;
}