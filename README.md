# porosity

## Getting Started

Platform         | Status
-----------------|-----------
Windows          | [![Build Status](https://comae.visualstudio.com/_apis/public/build/definitions/13b58962-60a0-48ed-879d-f56575385e2e/4/badge)](https://comae.visualstudio.com/_apis/public/build/definitions/13b58962-60a0-48ed-879d-f56575385e2e/4/badge)

## Overview
Ethereum is gaining a significant popularity in the blockchain community, mainly due to fact that it is design in a way that enables developers to write decentralized applications (Dapps) and smart-contract using blockchain technology.


Ethereum blockchain is a consensus-based globally executed virtual machine, also referred as  Ethereum Virtual Machine (EVM) by implemented its own micro-kernel supporting a handful number of instructions, its own stack, memory and storage. This enables the radical new concept of distributed applications.


Contracts live on the blockchain in an Ethereum-specific binary format (EVM bytecode). However, contracts are typically written in some high-level language such as Solidity and then compiled into byte code to be uploaded on the blockchain. Solidity is a contract-oriented, high-level language whose syntax is similar to that of JavaScript.


This new paradigm of applications opens the door to many possibilities and opportunities. Blockchain is often referred as secure by design, but now that blockchains can embed applications this raise multiple questions regarding architecture, design, attack vectors and patch deployments.


As we, reverse engineers, know having access to source code is often a luxury. Hence, the need for an open-source tool like Porosity: decompiler for EVM bytecode into readable Solidity-syntax contracts – to enable static and dynamic analysis of compiled contracts but also vulnerability discovery.

## Solidity
### Compilation
```
PS E:\defcon2017> more .\vulnerable.sol
contract SendBalance {
    mapping ( address => uint ) userBalances ;
    bool withdrawn = false ;

    function getBalance (address u) constant returns ( uint ){
        return userBalances [u];
    }

    function addToBalance () {
        userBalances[msg.sender] += msg.value ;
    }

    function withdrawBalance (){
        if (!(msg.sender.call.gas(0x1111).value (
            userBalances [msg . sender ])())) { throw ; }
        userBalances [msg.sender ] = 0;
    }
}
PS E:\defcon2017> & $solc --abi -o output vulnerable.sol
PS E:\defcon2017> & $solc --bin -o output vulnerable.sol
PS E:\defcon2017> & $solc --bin-runtime -o output vulnerable.sol
PS E:\defcon2017> $abi = Get-Content .\output\SendBalance.abi
PS E:\defcon2017> $bin = Get-Content .\output\SendBalance.bin
PS E:\defcon2017> $binRuntime = Get-Content .\output\SendBalance.bin-runtime
PS E:\defcon2017> $abi
[{"constant":false,"inputs":[],"name":"withdrawBalance","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"addToBalance","outputs":[],"type":"function"},{"constant":true,"inputs":[{"name":"u","type":"address"}],"name":"ge
tBalance","outputs":[{"name":"","type":"uint256"}],"type":"function"}]
PS E:\defcon2017> $bin
60606040526000600160006101000a81548160ff021916908302179055506101bb8061002b6000396000f360606040526000357c0100000000000000000000000000000000000000000000000000000000900480635fd8c7101461004f578063c0e317fb1461005e578063f8b2cb4f1461006d5761004d56
5b005b61005c6004805050610099565b005b61006b600480505061013e565b005b610083600480803590602001909190505061017d565b6040518082815260200191505060405180910390f35b3373ffffffffffffffffffffffffffffffffffffffff16611111600060005060003373ffffffffffffffff
ffffffffffffffffffffffff16815260200190815260200160002060005054604051809050600060405180830381858888f19350505050151561010657610002565b6000600060005060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055505b
565b34600060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055505b565b6000600060005060008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506101b6
565b91905056
PS E:\defcon2017> $binRuntime
60606040526000357c0100000000000000000000000000000000000000000000000000000000900480635fd8c7101461004f578063c0e317fb1461005e578063f8b2cb4f1461006d5761004d565b005b61005c6004805050610099565b005b61006b600480505061013e565b005b61008360048080359060
2001909190505061017d565b6040518082815260200191505060405180910390f35b3373ffffffffffffffffffffffffffffffffffffffff16611111600060005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054604051809050600060405180
830381858888f19350505050151561010657610002565b6000600060005060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055505b565b34600060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020
016000206000828282505401925050819055505b565b6000600060005060008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506101b6565b91905056
```

## Porosity
### Disassemble
```
PS E:\defcon2017> & $porosity --abi '[{\"constant\":false,\"inputs\":[],\"name\":\"withdrawBalance\",\"outputs\":[],\"type\":\"function\"},{\"constant\":false,\"inputs\":[],\"name\":\"addToBalance\",\"outputs\":[],\"type\":\"function\"},{\"
constant\":true,\"inputs\":[{\"name\":\"u\",\"type\":\"address\"}],\"name\":\"getBalance\",\"outputs\":[{\"name\":\"\",\"type\":\"uint256\"}],\"type\":\"function\"}]' --runtime-code 60606040526000357c0100000000000000000000000000000000000000
000000000000000000900480635fd8c7101461004f578063c0e317fb1461005e578063f8b2cb4f1461006d5761004d565b005b61005c6004805050610099565b005b61006b600480505061013e565b005b610083600480803590602001909190505061017d565b6040518082815260200191505060405180
910390f35b3373ffffffffffffffffffffffffffffffffffffffff16611111600060005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054604051809050600060405180830381858888f19350505050151561010657610002565b600060006000
5060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055505b565b34600060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055505b565b60006000600050
60008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506101b6565b91905056 --disassm
Contract::assignXrefToBlocks: function @ 0x00000000 (hash = 0x5fd8c710)
Contract::assignXrefToBlocks: function @ 0x00000033 (hash = 0xc0e317fb)
Contract::assignXrefToBlocks: function @ 0x0000003E (hash = 0xf8b2cb4f)
Contract::assignXrefToBlocks: branch @ 0x00000049
Contract::assignXrefToBlocks: branch @ 0x0000004F
Contract::assignXrefToBlocks: branch @ 0x0000005E
Contract::assignXrefToBlocks: branch @ 0x0000006D
Contract::assignXrefToBlocks: function @ 0x00000099 (hash = 0x00000000)
Contract::assignXrefToBlocks: branch @ 0x00000102
Contract::assignXrefToBlocks: branch @ 0x0000013C
Contract::assignXrefToBlocks: branch @ 0x0000017B
Contract::assignXrefToBlocks: branch @ 0x0000017D
Contract::assignXrefToBlocks: branch @ 0x000001B6
Instruction Copy (start = 0x0, end = 0x33)
Instruction Copy (start = 0x33, end = 0x3e)
Instruction Copy (start = 0x3e, end = 0x49)
Instruction Copy (start = 0x49, end = 0x4d)
Instruction Copy (start = 0x4d, end = 0x4f)
Instruction Copy (start = 0x4f, end = 0x5c)
Instruction Copy (start = 0x5c, end = 0x5e)
Instruction Copy (start = 0x5e, end = 0x6b)
Instruction Copy (start = 0x6b, end = 0x6d)
Instruction Copy (start = 0x6d, end = 0x83)
Instruction Copy (start = 0x83, end = 0x99)
Instruction Copy (start = 0x99, end = 0x102)
Instruction Copy (start = 0x102, end = 0x106)
Instruction Copy (start = 0x106, end = 0x13c)
Instruction Copy (start = 0x13c, end = 0x13e)
Instruction Copy (start = 0x13e, end = 0x17b)
Instruction Copy (start = 0x17b, end = 0x17d)
Instruction Copy (start = 0x17d, end = 0x1b6)
Instruction Copy (start = 0x1b6, end = 0x1bb)
Attempting to parse ABI definition:
[{"constant":false,"inputs":[],"name":"withdrawBalance","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"addToBalance","outputs":[],"type":"function"},{"constant":true,"inputs":[{"name":"u","type":"address"}],"name":"ge
tBalance","outputs":[{"name":"","type":"uint256"}],"type":"function"}]
Success.
Contract::setABI: Name: withdrawBalance()
Contract::setABI: signature: 0x5fd8c710
Contract::setABI: Name: addToBalance()
Contract::setABI: signature: 0xc0e317fb
Contract::setABI: Name: getBalance(address)
Contract::setABI: signature: 0xf8b2cb4f
    Porosity. v0.0.1 (Feb 2017) - NOT FOR RELEASE
    A decompiler for blockchain-based smart contract bytecode.
    Matt Suiche - m@comae.io

- Total byte code size: 0x1bb (443)


loc_00000000:
0x00000000 60 60                      PUSH1 60
0x00000002 60 40                      PUSH1 40
0x00000004 52                         MSTORE
0x00000005 60 00                      PUSH1 00
0x00000007 35                         CALLDATALOAD
0x00000008 7c 00  00  00  00  +      PUSH29 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 01
0x00000026 90                         SWAP1
0x00000027 04                         DIV
0x00000028 80                         DUP1
0x00000029 63 10  c7  d8  5f          PUSH4 10 c7 d8 5f
0x0000002e 14                         EQ
0x0000002f 61 4f  00                  PUSH2 4f 00
0x00000032 57                         JUMPI

loc_00000033:
0x00000033 80                         DUP1
0x00000034 63 fb  17  e3  c0          PUSH4 fb 17 e3 c0
0x00000039 14                         EQ
0x0000003a 61 5e  00                  PUSH2 5e 00
0x0000003d 57                         JUMPI

loc_0000003e:
0x0000003e 80                         DUP1
0x0000003f 63 4f  cb  b2  f8          PUSH4 4f cb b2 f8
0x00000044 14                         EQ
0x00000045 61 6d  00                  PUSH2 6d 00
0x00000048 57                         JUMPI

loc_00000049:
0x00000049 61 4d  00                  PUSH2 4d 00
0x0000004c 56                         JUMP

loc_0000004d:
0x0000004d 5b                         JUMPDEST
0x0000004e 00                         STOP

withdrawBalance():
0x0000004f 5b                         JUMPDEST
0x00000050 61 5c  00                  PUSH2 5c 00
0x00000053 60 04                      PUSH1 04
0x00000055 80                         DUP1
0x00000056 50                         POP
0x00000057 50                         POP
0x00000058 61 99  00                  PUSH2 99 00
0x0000005b 56                         JUMP

loc_0000005c:
0x0000005c 5b                         JUMPDEST
0x0000005d 00                         STOP

addToBalance():
0x0000005e 5b                         JUMPDEST
0x0000005f 61 6b  00                  PUSH2 6b 00
0x00000062 60 04                      PUSH1 04
0x00000064 80                         DUP1
0x00000065 50                         POP
0x00000066 50                         POP
0x00000067 61 3e  01                  PUSH2 3e 01
0x0000006a 56                         JUMP

loc_0000006b:
0x0000006b 5b                         JUMPDEST
0x0000006c 00                         STOP

getBalance(address):
0x0000006d 5b                         JUMPDEST
0x0000006e 61 83  00                  PUSH2 83 00
0x00000071 60 04                      PUSH1 04
0x00000073 80                         DUP1
0x00000074 80                         DUP1
0x00000075 35                         CALLDATALOAD
0x00000076 90                         SWAP1
0x00000077 60 20                      PUSH1 20
0x00000079 01                         ADD
0x0000007a 90                         SWAP1
0x0000007b 91                         SWAP2
0x0000007c 90                         SWAP1
0x0000007d 50                         POP
0x0000007e 50                         POP
0x0000007f 61 7d  01                  PUSH2 7d 01
0x00000082 56                         JUMP

loc_00000083:
0x00000083 5b                         JUMPDEST
0x00000084 60 40                      PUSH1 40
0x00000086 51                         MLOAD
0x00000087 80                         DUP1
0x00000088 82                         DUP3
0x00000089 81                         DUP2
0x0000008a 52                         MSTORE
0x0000008b 60 20                      PUSH1 20
0x0000008d 01                         ADD
0x0000008e 91                         SWAP2
0x0000008f 50                         POP
0x00000090 50                         POP
0x00000091 60 40                      PUSH1 40
0x00000093 51                         MLOAD
0x00000094 80                         DUP1
0x00000095 91                         SWAP2
0x00000096 03                         SUB
0x00000097 90                         SWAP1
0x00000098 f3                         RETURN

loc_00000099:
0x00000099 5b                         JUMPDEST
0x0000009a 33                         CALLER
0x0000009b 73 ff  ff  ff  ff  +      PUSH20 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0x000000b0 16                         AND
0x000000b1 61 11  11                  PUSH2 11 11
0x000000b4 60 00                      PUSH1 00
0x000000b6 60 00                      PUSH1 00
0x000000b8 50                         POP
0x000000b9 60 00                      PUSH1 00
0x000000bb 33                         CALLER
0x000000bc 73 ff  ff  ff  ff  +      PUSH20 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0x000000d1 16                         AND
0x000000d2 81                         DUP2
0x000000d3 52                         MSTORE
0x000000d4 60 20                      PUSH1 20
0x000000d6 01                         ADD
0x000000d7 90                         SWAP1
0x000000d8 81                         DUP2
0x000000d9 52                         MSTORE
0x000000da 60 20                      PUSH1 20
0x000000dc 01                         ADD
0x000000dd 60 00                      PUSH1 00
0x000000df 20                         SHA3
0x000000e0 60 00                      PUSH1 00
0x000000e2 50                         POP
0x000000e3 54                         SLOAD
0x000000e4 60 40                      PUSH1 40
0x000000e6 51                         MLOAD
0x000000e7 80                         DUP1
0x000000e8 90                         SWAP1
0x000000e9 50                         POP
0x000000ea 60 00                      PUSH1 00
0x000000ec 60 40                      PUSH1 40
0x000000ee 51                         MLOAD
0x000000ef 80                         DUP1
0x000000f0 83                         DUP4
0x000000f1 03                         SUB
0x000000f2 81                         DUP2
0x000000f3 85                         DUP6
0x000000f4 88                         DUP9
0x000000f5 88                         DUP9
0x000000f6 f1                         CALL
0x000000f7 93                         SWAP4
0x000000f8 50                         POP
0x000000f9 50                         POP
0x000000fa 50                         POP
0x000000fb 50                         POP
0x000000fc 15                         ISZERO
0x000000fd 15                         ISZERO
0x000000fe 61 06  01                  PUSH2 06 01
0x00000101 57                         JUMPI

loc_00000102:
0x00000102 61 02  00                  PUSH2 02 00
0x00000105 56                         JUMP

loc_00000106:
0x00000106 5b                         JUMPDEST
0x00000107 60 00                      PUSH1 00
0x00000109 60 00                      PUSH1 00
0x0000010b 60 00                      PUSH1 00
0x0000010d 50                         POP
0x0000010e 60 00                      PUSH1 00
0x00000110 33                         CALLER
0x00000111 73 ff  ff  ff  ff  +      PUSH20 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0x00000126 16                         AND
0x00000127 81                         DUP2
0x00000128 52                         MSTORE
0x00000129 60 20                      PUSH1 20
0x0000012b 01                         ADD
0x0000012c 90                         SWAP1
0x0000012d 81                         DUP2
0x0000012e 52                         MSTORE
0x0000012f 60 20                      PUSH1 20
0x00000131 01                         ADD
0x00000132 60 00                      PUSH1 00
0x00000134 20                         SHA3
0x00000135 60 00                      PUSH1 00
0x00000137 50                         POP
0x00000138 81                         DUP2
0x00000139 90                         SWAP1
0x0000013a 55                         SSTORE
0x0000013b 50                         POP

loc_0000013c:
0x0000013c 5b                         JUMPDEST
0x0000013d 56                         JUMP

loc_0000013e:
0x0000013e 5b                         JUMPDEST
0x0000013f 34                         CALLVALUE
0x00000140 60 00                      PUSH1 00
0x00000142 60 00                      PUSH1 00
0x00000144 50                         POP
0x00000145 60 00                      PUSH1 00
0x00000147 33                         CALLER
0x00000148 73 ff  ff  ff  ff  +      PUSH20 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0x0000015d 16                         AND
0x0000015e 81                         DUP2
0x0000015f 52                         MSTORE
0x00000160 60 20                      PUSH1 20
0x00000162 01                         ADD
0x00000163 90                         SWAP1
0x00000164 81                         DUP2
0x00000165 52                         MSTORE
0x00000166 60 20                      PUSH1 20
0x00000168 01                         ADD
0x00000169 60 00                      PUSH1 00
0x0000016b 20                         SHA3
0x0000016c 60 00                      PUSH1 00
0x0000016e 82                         DUP3
0x0000016f 82                         DUP3
0x00000170 82                         DUP3
0x00000171 50                         POP
0x00000172 54                         SLOAD
0x00000173 01                         ADD
0x00000174 92                         SWAP3
0x00000175 50                         POP
0x00000176 50                         POP
0x00000177 81                         DUP2
0x00000178 90                         SWAP1
0x00000179 55                         SSTORE
0x0000017a 50                         POP

loc_0000017b:
0x0000017b 5b                         JUMPDEST
0x0000017c 56                         JUMP

loc_0000017d:
0x0000017d 5b                         JUMPDEST
0x0000017e 60 00                      PUSH1 00
0x00000180 60 00                      PUSH1 00
0x00000182 60 00                      PUSH1 00
0x00000184 50                         POP
0x00000185 60 00                      PUSH1 00
0x00000187 83                         DUP4
0x00000188 73 ff  ff  ff  ff  +      PUSH20 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0x0000019d 16                         AND
0x0000019e 81                         DUP2
0x0000019f 52                         MSTORE
0x000001a0 60 20                      PUSH1 20
0x000001a2 01                         ADD
0x000001a3 90                         SWAP1
0x000001a4 81                         DUP2
0x000001a5 52                         MSTORE
0x000001a6 60 20                      PUSH1 20
0x000001a8 01                         ADD
0x000001a9 60 00                      PUSH1 00
0x000001ab 20                         SHA3
0x000001ac 60 00                      PUSH1 00
0x000001ae 50                         POP
0x000001af 54                         SLOAD
0x000001b0 90                         SWAP1
0x000001b1 50                         POP
0x000001b2 61 b6  01                  PUSH2 b6 01
0x000001b5 56                         JUMP

loc_000001b6:
0x000001b6 5b                         JUMPDEST
0x000001b7 91                         SWAP2
0x000001b8 90                         SWAP1
0x000001b9 50                         POP
0x000001ba 56                         JUMP
PS E:\defcon2017>
```
### Decompilation
```
PS E:\defcon2017> & $porosity --abi '[{\"constant\":false,\"inputs\":[],\"name\":\"withdrawBalance\",\"outputs\":[],\"type\":\"function\"},{\"constant\":false,\"inputs\":[],\"name\":\"addToBalance\",\"outputs\":[],\"type\":\"function\"},{\"
constant\":true,\"inputs\":[{\"name\":\"u\",\"type\":\"address\"}],\"name\":\"getBalance\",\"outputs\":[{\"name\":\"\",\"type\":\"uint256\"}],\"type\":\"function\"}]' --runtime-code 60606040526000357c0100000000000000000000000000000000000000
000000000000000000900480635fd8c7101461004f578063c0e317fb1461005e578063f8b2cb4f1461006d5761004d565b005b61005c6004805050610099565b005b61006b600480505061013e565b005b610083600480803590602001909190505061017d565b6040518082815260200191505060405180
910390f35b3373ffffffffffffffffffffffffffffffffffffffff16611111600060005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054604051809050600060405180830381858888f19350505050151561010657610002565b600060006000
5060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055505b565b34600060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055505b565b60006000600050
60008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506101b6565b91905056 --decompile --verbose 0
Attempting to parse ABI definition:
[{"constant":false,"inputs":[],"name":"withdrawBalance","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"addToBalance","outputs":[],"type":"function"},{"constant":true,"inputs":[{"name":"u","type":"address"}],"name":"ge
tBalance","outputs":[{"name":"","type":"uint256"}],"type":"function"}]
Success.
    Porosity. v0.0.1 (Feb 2017) - NOT FOR RELEASE
    A decompiler for blockchain-based smart contract bytecode.
    Matt Suiche - m@comae.io

Hash: 0x5FD8C710
function withdrawBalance() {
      if (msg.sender.call.gas(4369).value(store[msg.sender])()) {
         store[msg.sender] = 0x0;
      }
}


L3 (D8193): Potential reetrant vulnerability found.

LOC: 5
Hash: 0xC0E317FB
VMState::executeInstruction: NOT_IMPLEMENTED: CALLVALUE
function addToBalance() {
      store[msg.sender] = store[msg.sender];
      return;
}


LOC: 4
Hash: 0xF8B2CB4F
function getBalance(address) {
      return store[arg_4];
}


LOC: 3
PS E:\defcon2017>
```