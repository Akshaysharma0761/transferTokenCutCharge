
   // 1. Create an ERC20 token that supports fees on token transfer.
   // Put your own creative thoughts on how you manage this fee such that
   // it would be beneficial for the token life cycle or it's holders.
 
 //SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;
interface IERC20
{
   function totalSupply()external view returns(uint);
   function balanceOf(address account) external view returns(uint);
   function transfer(address recipent, uint amount) external payable returns(bool);
   function allowance(address owner, address spender)external view returns(uint);
   function approve(address spender,uint amount)external returns(bool);
   function transferFrom(address sender,address recipent,uint amount)external returns(bool);

event Transfer(address indexed from,address indexed to,uint amount);
event Approve(address indexed owner,address indexed spender,uint amount);

}
contract ERC21 is IERC20{
    uint public totalSupply;
    address public owner;
    address  payable[] public participants;
    mapping(address=>uint)public balanceOf;
    mapping(address=>mapping(address=>uint)) public allowance;
    string public name='akshay';
    string public symbol='sharma';
    uint8 public decimals=18;
    constructor(uint _totalSupply)public{
        totalSupply=_totalSupply;
        balanceOf[msg.sender]=totalSupply;
        owner=msg.sender;
    }
     function getcontractBalance()public view  returns(uint){
         require(msg.sender==owner);
        return address(this).balance;
     }
    function transfer(address recipent,uint amount) external payable returns(bool) {
         require(msg.value==1 ether);
         participants.push(payable(msg.sender));
        balanceOf[msg.sender]-=amount;
        balanceOf[recipent]+=amount;
        emit Transfer(msg.sender,recipent,amount);
        return true;
    }
    
   function approve(address spender,uint amount)external returns(bool){
       allowance[msg.sender][spender]=amount;
       emit Approve(msg.sender,spender,amount);
       return true;
   }
    function transferFrom(address sender,address recipent,uint amount)external returns(bool){
        allowance[sender][msg.sender]-=amount;
        balanceOf[msg.sender]-=amount;
        balanceOf[recipent]+=amount;
        emit Transfer(msg.sender,recipent,amount);
        return true;

    }
    function checkbalances(address account) external  returns(uint) {
    balanceOf[account];
   }
   function transferContractAmountToOwner() public{
       require(msg.sender==owner);
       address payable owners=payable(owner);
       owners.transfer(getcontractBalance());
       

   }

}
