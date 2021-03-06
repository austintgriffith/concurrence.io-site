---
title: "Responses"
date: 2017-09-21T06:55:00-06:00
---
The **Responses** contract is the datastore for **responses** *(bytes32)*. As miners perform their duties, they call **addResponse()** to deliver their results to the fleet. This list of **responses** *(bytes32)* is tracked in a [linked list](/exploration/linkedlists/) that allows the **combiner** contracts to quickly traverse through results. Notice that whatever complex work the miner does off-chain, it must result in a single *bytes32* response on-chain.

<img src="/images/responses.png"/>


```
pragma solidity ^0.4.11;

contract Responses is HasNoEther, Addressed {

  function Responses(address _mainAddress) Addressed(_mainAddress) { }

  event AddResponse(address indexed sender, bytes32 indexed request, bytes32 indexed id, bytes32 response, uint256 count);

  uint256 public count = 0;

  struct Response{
    address miner;
    bytes32 response;
    bytes32 next;
  }

  mapping (bytes32 => bytes32) public heads;
  mapping (bytes32 => Response) public responses;

  function addResponse(bytes32 _request,bytes32 _response) public returns (bool) {

    //TODO you shouldn't be able to add a response until the combiner is 'open'

    Response memory response = Response(msg.sender,_response,heads[_request]);

    bytes32 id = sha3(now,count,response.miner,response.response);
    responses[id] = response;
    heads[_request] = id;

    AddResponse(msg.sender,_request,id,responses[id].response,count);

    count=count+1;

    return true;
  }

  function getResponse(bytes32 id) public constant returns (address,bytes32,bytes32) {
    return (responses[id].miner,responses[id].response,responses[id].next);
  }

  function reset(bytes32 id) public returns (bool) {
    //TODO
    //THE COMBINER FOR THE REQUEST SHOULD BE ABLE TO RUN THIS
    //IT SHOULD SET THE HEAD FOR THE REQUEST TO 0
  }

}

import 'zeppelin-solidity/contracts/ownership/HasNoEther.sol';
import 'Addressed.sol';

```
Current address ( http://relay.concurrence.io/address/Responses ):
```
0x72cFe4BA5E3e3C3388502178c5E289885c8c788a
```
Current ABI ( http://relay.concurrence.io/abi/Responses ):
```
[{"constant":true,"inputs":[],"name":"count","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"mainAddress","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"","type":"bytes32"}],"name":"heads","outputs":[{"name":"","type":"bytes32"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"id","type":"bytes32"}],"name":"getResponse","outputs":[{"name":"","type":"address"},{"name":"","type":"bytes32"},{"name":"","type":"bytes32"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"","type":"bytes32"}],"name":"responses","outputs":[{"name":"miner","type":"address"},{"name":"response","type":"bytes32"},{"name":"next","type":"bytes32"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"reclaimEther","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_request","type":"bytes32"},{"name":"_response","type":"bytes32"}],"name":"addResponse","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_mainAddress","type":"address"}],"name":"setMainAddress","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"id","type":"bytes32"}],"name":"reset","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[{"name":"_mainAddress","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":false,"stateMutability":"nonpayable","type":"fallback"},{"anonymous":false,"inputs":[{"indexed":true,"name":"sender","type":"address"},{"indexed":true,"name":"request","type":"bytes32"},{"indexed":true,"name":"id","type":"bytes32"},{"indexed":false,"name":"response","type":"bytes32"},{"indexed":false,"name":"count","type":"uint256"}],"name":"AddResponse","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"}]
```
