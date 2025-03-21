---
title: DefiHacklabs-Solidity殘酷共學
published: 2024-10-10
description: "殘酷共學的筆記，很爛不要看"
tags: ["solidity"]
category: meow
draft: false
---

內容極水，不要拿我的內容當作學習教材

爛到我都不想花時間整理

### 2024.09.23
1. Remix IDE
* File Extension of Solidity : `.sol`
* `Ctrl` + `S` -> Compile
* `Deploy` -> Simulate Ethereum chain -> run smart contracts
2. Basic
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.21;
contract HelloWeb3{
    string public _string = "Hello Web3!";
}
```
- `// SPDX-License-Identifier: MIT` -> Denotes license identifier (`MIT` for example)
- `pragma solidity ^0.8.21;` -> Only allow compiler version `0.8.21` ~ `0.9.0`(`0.8.21` for example)
- `contract HelloWeb3` -> Similar as `class` in other programming language (`HelloWeb3` for example). also,we call it "合約"
- `string public _string = "Hello Web3!";` -> Declared Variables
-  `string` : Value type 
    
| Type    | Explain                     | Example         |
| ------- | --------------------------- | --------------- |
| int     | integer                     | 3 , 300 , -123  |
| uint    | unsigned integer(include 0) | 3 , 300         |
| uint<k> | k-bit unsigned integer      | uint8 , uint64  |
| bool    | boolean                     | true , false    |
| address | address(e.g. ETH account)   | 0x1234556       |
| enum    | enumeration                 | {Mon=1,Tue,Wed} |

* `public` : visibility 
    

| Type                | Outside Contract | In Contract         | Inheritance Contract |
| ------------------- |:----------------:| ------------------- |:--------------------:|
| public              |        ✅        | ✅                  |          ✅          |
| private             |        ❌        | ✅                  |          ❌          |
| external (function) |        ✅        | ✅use `this.func()` | ✅use `this.func()`  |
| internal            |        ❌        | ✅                  |          ✅          |
- `_string` : Value name,Define by your self
- `"Hello Web3!"` : Value

---

```solidity=
function <functionName>(<Type> <ParameterName>) <visibility> <modifier> returns (<Type>) 
```

`<Type> <parameter1>` : Input parameter,e.g. `int a`

Also,you can input more than 1 parameter
    
`<modifier>` : 


| Value   | Explain                   |
| ------- | ------------------------- |
| pure    | cannot read nor write     |
| view    | can read but cannot write |
| (NULL)  | can both read and write   |
| payable | can receive ETH           |

    
### 2024.09.24
1. Variables
    
data storage locations:
| Type             | stored    | modified      |
| ---------------- | --------- | ------------- |
| storage(deafult) | on-chain  | ✅            |
| memory           | in memory | ✅            |
| calldata         | in memory | ❌(read-only) |


scope:
```solidity=
contract Example {
    uint public count;
    function increment() public pure returns (uint) {
        uint localCount = 0; 
        return localCount;
    }
}
```
| Type             | Location    | Complain/Example            |
| ---------------- | ----------- | --------------------------- |
| State(狀態變量)   | `storage`    | `count`(in contract storage)        |
| Local(局部變量)   | `memory`(reference types)<br>`stack`(basic types)      | `localCount`(in function)   |
| Global(全局變量)  | EVM dynamic | `msg.sender`,`block.number` |

2. Array
```solidity=
//fixed-sized arrays
<type>[<length>] <name>;
<type>[] memory <name> = new <type>[](<length>);
uint[9] array;
uint[] memory array3 = new uint[](5);

//dynamic-sized array
uint[] array1;
bytes array2;    //dynamic byte array
```
- `.length` -> return array's length
dynamic array :
- `.push` -> add `0`element at the end of array
- `.puxh(x)` -> add `x`element at the end of array
- `.pop` -> remove the last element of array

3. Struct
```solidity=
struct Student{
    uint id;
    uint score; 
}
//4 ways to assign value
function method1() external{
    Student storage _student = student; //copy Student
    _student.id = 11;
    _student.score = 100;
}
function method2() external{
    student.id = 1;
    student.score = 80;
}
function method3() external {
    student = Student(3, 90);
}
function method4() external {
    student = Student({id: 4, score: 60});
}
```
4. Map
key -> Value
```solidity=
mapping(<KeyType> => <ValueType>) public <name>;
mapping(address => uint) public balances;

<name>[<key>] = <Value>;
balances[msg.sender] = 100;
```

    
### 2024.09.25

1. constant/immutable

| Characteristic      | `constant`                                 | `immutable`                                                       |
| ------------------- | ------------------------------------------ | ----------------------------------------------------------------- |
| Initialization time | declaration                                | constructor/declaration                                           |
| Modification time   | Compile-time constant                      | Determined at deployment                                          |
| Storage location    | Inlined directly at compile-time           | storage                                                           |
| Use cases           | completely fixed values (e.g. fixed rates) | values determined by conditions at deployment time (e.g.deployer) |


2. Control flow
same as C
```solidity=
//if-else
function ifElseTest(uint256 _number) public pure returns(bool){
    if(_number == 0){
        return(true);
    }else{
        return(false);
    }
}
//for loop
function forLoopTest() public pure returns(uint256){
    uint sum = 0;
    for(uint i = 0; i < 10; i++){
        sum += i;
    }
    return(sum);
}
//while
function doWhileTest() public pure returns(uint256){
    uint sum = 0;
    uint i = 0;
    do{
        sum += i;
        i++;
    }while(i < 10);
    return(sum);
}
//Insertion Sort
function insertionSort(uint[] memory a) public pure returns(uint[] memory) {
    for (uint i = 1;i < a.length;i++){
        uint temp = a[i];
        //uint j=i-1;
        uint j=i;    //uint can
        while( (j >= 1) && (temp < a[j-1])){    
        (temp < a[j])
            a[j] = a[j-1];
            j--;
        }
        a[j] = temp;
    }
    return(a);
}
```

        

### 2024.09.27
1. constructor		
If variables is dynamic dicision **when deploy**,we can use `constructor` to assign
(similar as `decorator`)
```solidity=
address owner; 
constructor(address initialOwner) {
    owner = initialOwner; 
}
```
2. modifier
use to modify the behavior of function
```solidity=
address public owner;
bool public paused;

constructor() {
	owner = msg.sender;
	paused = false;
}

modifier onlyOwner() {
	require(msg.sender == owner, "Not authorized");
	_;//continue to run function
}

modifier whenNotPaused() {
	require(!paused, "Contract is paused");
	_;
}

function setPause(bool _paused) public onlyOwner {
	paused = _paused;
}

function changeOwner(address newOwner) public onlyOwner whenNotPaused {
	owner = newOwner;
}
```
3. event
When event be triggered,it will be recorded in log

```solidity=
event Transfer(address indexed from, address indexed to, uint256 value);

function transfer(address _to, uint256 _value) public {
    emit Transfer(msg.sender, _to, _value);    //trigger event
}
```
4. override
OverWrite the parent function
```solidity=
contract Parent {
    function foo() public virtual {
    }
}

contract Child is Parent {
    function foo() public override {
        //...
    }
}
```
		
### 2024.09.29
1. Interface
Define the interactions between contracts
```solidity=
interface Token {
    function transfer(address recipient, uint256 amount) external returns (bool);
}

contract MyToken is Token {
    mapping(address => uint256) public balances;

    function transfer(address recipient, uint256 amount) external override returns (bool) {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        balances[recipient] += amount;
        return true;
    }
}

```
1.1  IERC721

- event-Transfer:
```solidity!
event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
```
when `tokenID` moved from `from` to another address `to`

- event-ApprovalForAll:
```solidity!
event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
```
The token holder authorizes/de-authorizes an `operator` to manage all its tokens.

- function-balanceOf
```solidity!
function balanceOf(address owner) external view returns (uint256 balance);
```
return the number of tokens owned by `owner`

- function-ownerOf
```solidity!
function ownerOf(uint256 tokenId) external view returns (address owner);
```
return the address of owner of `tokenID`

and more....

        
### 2024.09.30

1. Error Handling
- `require`
Used to check if a condition is true
If false,contract will be terminated
```solidity=
function withdraw(uint amount) public {
    require(amount <= balance, "Insufficient balance");
    balance -= amount;
    payable(msg.sender).transfer(amount);
}
```
        
- `assert`
Used to check if a condition **always** be true
```solidity=
function decrement() public {
    assert(counter > 0);
    counter -= 1;
}
```

- `error`
Custom error
```solidity=
error InsufficientBalance(uint requested, uint available);

function withdraw(uint amount) public {
    if (amount > balance)
        revert InsufficientBalance({
            requested: amount,
            available: balance
        });
    balance -= amount;
    payable(msg.sender).transfer(amount);
}
```
### 2024.10.02
#### overloading
You can define 2 or more same-name function (except `modifier`)<br>
but need to be different input type
```solidity=
function saySomething() public pure returns(string memory){
    return("Nothing");
}

function saySomething(string memory something) public pure returns(string memory){
    return(something);
}
```
#### Argument Matching
```solidity=
function f(uint8 _in) public pure returns (uint8 out) {
    out = _in;
}

function f(uint256 _in) public pure returns (uint256 out) {
    out = _in;
}
```
if we call `f(123)`,it can be `uint8` and`uint256`,the compiler will report error

### 2024.10.03
#### Library
Like "Header file" in C++ , a function collection
e.g. `Strings`
```solidity=
using Strings for uint256;
function getString1(uint256 _number) public pure returns(string memory){
    return _number.toHexString();
}
```
or
```solidity=
function getString2(uint256 _number) public pure returns(string memory){
    return Strings.toHexString(_number);
}
```
tips...
- Non-storable state variable
- Unable to receive ETH
- Can't be destroyed

#### Import
If we have...
```
Dir
├── Import.sol
└── Yeye.sol
```
We can use `import` to include contracts/library... in external file
```solidity!
import './Yeye.sol';
```
other option:
```solidity!
//only a part that needed 
import { ContractName1, ContractName2 } from "path/to/file.sol";

//as to rename
import { ContractName as Alias } from "path/to/file.sol";

//package manager
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

//URL
import "https://github.com/username/repo/blob/main/Contract.sol";
```

### 2024.10.04
#### receive ETH
```solidity=
event Received(address Sender, uint Value);
receive() external payable {
    emit Received(msg.sender, msg.value);
}
```
- can't have any parameters
- can't return anything
- need to be `external` and `payable`

#### fallback
- If the contract only has `receive` && transaction doesn't have calldata -> receive()
- If the contract has `fallback` && (transaction has calldata || no corresponding function) -> fallback()

### 2024.10.06
#### Send ETH
##### send/transfer
- Only 2300 gas per transfer
```solidity=
//recipient -> recipient's address
recipient.transfer(1 ether);
//if failed , it will revert automatically

bool success = recipient.send(1 ether);
//if failed , it won't revert
if (!success) {
    //if transfer failed...
}
```
##### call
```solidity=
(bool success, ) = recipient.call{value: 1 ether}("");
if(!success) {
	//if transfer failed...
}
```

### 2024.10.08
#### Calling via Contract Address:

Use the contract address to create a reference and call the target contract's function.<br>
```solidity=
function callSetX(address _Address, uint256 x) external {
    OtherContract(_Address).setX(x);
}
```
#### Passing Contract Reference:

Pass the target contract reference directly and call its functions.<br>
```solidity=
function callGetX(OtherContract _Address) external view returns (uint x) {
    x = _Address.getX();
}
```
#### Creating Contract Variable:

Create a contract variable within the function to call the target's functions.
```solidity=
Copy code
function callGetX2(address _Address) external view returns (uint x) {
    OtherContract oc = OtherContract(_Address);
    x = oc.getX();
}
```
#### Calling `payable` Function and Sending ETH:

Call a payable function and transfer ETH using {value: msg.value}.
```solidity=
Copy code
function setXTransferETH(address otherContract, uint256 x) payable external {
    OtherContract(otherContract).setX{value: msg.value}(x);
}
```
### 2024.10.10
#### delegatecall
- low-level function
- executes the code of another contract in the context of the calling contract.
```solidity=
contract LogicContract {
    uint public number;

    function setNumber(uint _num) public {
        number = _num;
    }
}

contract ProxyContract {
    uint public number;

    function setNumber(address _logicContract, uint _num) public {
        (bool success, ) = _logicContract.delegatecall(
            abi.encodeWithSignature("setNumber(uint256)", _num)
        );
        require(success, "delegatecall failed");
    }
}
```

### Afterall
最後有一天因為段考忘記寫筆記，就被踢掉了QQ

沒拿到學習證書= =