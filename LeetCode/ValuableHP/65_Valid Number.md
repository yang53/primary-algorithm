#  Valid Number
## 描述：
Validate if a given string is numeric.Some examples:
* "0" => true
* " 0.1 " => true
* "abc" => false
* "1 a" => false
* "2e10" => true
### Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one.
## 接口：
```C++
class Solution {
public:
    bool isNumber(string s) {
    }
};
```
## 分析：
### 1、题意解析：
根据题意，需要对输入字符串进行判断，是否为一个合法的数字，以下为合法数字的几种情况：
* 空格 + 数字 + 空格
* 空格 + 点 + 数字 + 空格
* 空格 + 符号（+，-） + 数字 + 空格
* 空格 + 符号（+，-） + 点 + 数字 + 空格
* 空格 + (1,2,3,4,...) + e/E + (1,2,3,4,...) + 空格
>#### 以上五种情况下可能出现的结果为：1、数字；2、只含空格的字符串；3、空字符串。
### 2、方法解析：
采用有限状态机来解决，初始状态为空字符串，每次根据输入的字符可以得到相应的状态变化，判断结束时候的状态是否为合法状态即可。不合法状态均标识为-1。
将空字符串和只含空格的字符串均视为初始状态。
### 3、状态转移：
* 状态0：“ ”
>* 输入空格：仍旧为0状态;
>* 输入数字：转变为1状态，状态1无法接受符号，其他的状态转移同状态0，并且1为合法的结束状态。
>* 输入点：转变为2状态，状态2必须接受数字，其他接受的状态转移均为不合法。
>* 输入符号：转变为3状态，状态3除了无法再接受符号外其余的状态转移同状态0。
>* 输入指数（e/E）：不合法。
* 状态1：“1 ”
>* 输入数字：仍旧为1状态；
>* 输入点：转换为状态4，可以接受空格、数字、指数，其他的接受均不合法。状态4为合法的结束状态。
>* 输入指数：转换为状态5，可以接受符号、数字，不能接受点，后面保证整数指数。后面必须再接受数字，所以是非法结束状态
* 状态2：“ .”
>* 输入数字：转换为状态4。其他的输入均不合法。
* 状态3：“+ ”
>* 输入空格：仍旧为3状态;
>* 输入数字：转变为1状态，状态1无法接受符号，其他的状态转移同状态0，并且1为合法的结束状态。
>* 输入点：转变为2状态，状态2必须接受数字，其他接受的状态转移均为不合法。
>* 输入符号：不合法。
>* 输入指数（e/E）：不合法。
* 状态4：“1.”
>* 输入空格：转换成状态8
>* 输入数字：仍旧为状态4
>* 输入指数：转换成状态5
* 状态5：“e ”
>* 输入符号：转换成状态6，不能再接受符号。
>* 输入数字：转换成状态7，状态7可以合法结束，并且只能接受数字和空格。
* 状态6：“e+”
>* 输入数字：转换为状态7。
* 状态7：可以接受空格，也可以接受数字，合法的结束状态
>* 输入空格：仍旧为状态7
>* 输入数字：转换为状态8
* 状态8：合法的结束状态，但是不能再接受除了空格外的输入
>* 输入空格：仍旧为状态8
### 4、AC代码：
```C++
bool isNumber(string s) {
		//输入参数枚举
		enum InputType {
			INVALID,//表示不正确
			SPACE,//表示空格
			SIGN,//表示符号
			DIGIT,//表示单个数字字符
			DOT,//表示点符号
			EXPONENT,//表示科学计算
			NUM_INPUTS//数字输入
		};
		//状态转移图，map[i][j]的值表示状态i在input下的下一个状态
		vector<vector<int>> vviMap = {
			{ -1,  0,  3,  1,  2, -1 },//状态0在每一种input下的下一个状态
			{ -1,  8, -1,  1,  4,  5 },//状态1在每一种input下的下一个状态
			{ -1, -1, -1,  4, -1, -1 },//状态2在每一种input下的下一个状态
			{ -1, -1, -1,  1,  2, -1 },//状态3在每一种input下的下一个状态
			{ -1,  8, -1,  4, -1,  5 },//状态4在每一种input下的下一个状态
			{ -1, -1,  6,  7, -1, -1 },//状态5只接受数字和符号输入
			{ -1, -1, -1,  7, -1, -1 },//状态6只接受数字输入
			{ -1,  8, -1,  7, -1, -1 },//状态7只接受空格、数字输入
			{ -1,  8, -1, -1, -1, -1 },//状态8只接受空格输入
		};
		int state = 0;//初始状态为状态0
		for (int i = 0;i < s.size();i++)
		{
			InputType inputType = INVALID;//初始化s[i]为不正确字符，后面根据实际字符对inputType赋值
			if (isspace(s[i]))
				inputType = SPACE;
			else if (s[i] == '+' || s[i] == '-')
				inputType = SIGN;
			else if (isdigit(s[i]))
				inputType = DIGIT;
			else if (s[i] == '.')
				inputType = DOT;
			else if (s[i] == 'e' || s[i] == 'E')
				inputType = EXPONENT;

			state = vviMap[state][inputType];
			if (state == -1)//一旦出现不合法状态，直接返回false
				return false;

		}
		return state == 1 || state == 4 || state == 7 || state == 8;
	}
```