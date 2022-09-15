## [L-01] IMMUTABLE ADDRESSES LACK ZERO-ADDRESS CHECK

Constructors should check the address written in an immutable address variable is not the zero address

There is 10 instance of this issue:

> **File : Token.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L30-L31
>
> **File : Manager.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L62-L66
>
> **File : Treasury.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L33
>
> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L41-L43
>
> **File : Auction.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L40-L41


#### **Mitigation**


## [L-02] ADDRESSES LACK ZERO-ADDRESS CHECK BEFORE ASSIGNING THEM TO STATE VARIABLE

Should check the addresses before assigning them to state variables

There is 2 instance of this issue:

> **File : Token.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L65-L66
>
> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L41-L43
>
> **File : Auction.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L79


#### **Mitigation**


## [L-03] _addFounders() IN Token.sol MAY LEAD TO DoS(block gas limit exceed) CONDITION


_addFounder() used ***Nested For loops on Dynamic Array*** inside which forther internal function calls occur which makes state change,
This is quite gas consuming, if length of Dynamic Array will too long, then it leads to Block gas limit exceed condition.

There is 1 instance of this issue:

> **File : Token.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L71-L125


## [L-04] _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function

There is 1 instance of this issue:

> **File : Token.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L161

#### **Mitigation**


## [L-05] _authorizeUpgrade() OF Manager.sol SHOULD DO SOMETHING, ATLEAST EMIT SOME EVENTS WHEN NEW IMPLEMENTED ADDRESS CHANGE


_authorizeUpgrade() function is just blank block which doing nothing,
At least it should emit a events when Implemented address changes   

> **File : Manager.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209-L210



## [L-06] execute() FROM Treasury.sol DOESN'T CHECK FOR DYNAMIC INPUT ARRAY LENGTH
3 dynamic array are take as input parameter and then used in a For loop,
There is no code syntax for checking 3 arrays are of same length or not. As a Human error Owner can copy paste array of different length that ultimately 
result in function fail.

> **File : Treasury.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L141-L172

#### **Mitigation**

## [L-07] CONSIDER ADDINGS CHECKS FOR SIGNATURE MALLEABILITY

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly

There is 1 instance of this issue:

> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L236


## [L-08] LACK OF UINT VARIIABLE CHECK

uint check absent for _reservePrice, during initialization in Auction contract 


> **File : Auction.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L78


## [L-09] CRITICAL SETTINGS CHANGED BY OWNER ANYTIME

In Auction contract critical settings (Important variables regarding auction) can be changed by owner anytime using functions like
setDuration(), setReservePrice(), setTimeBuffer(), setMinimumBidIncrement()


> **File : Auction.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307-L311
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315-L319
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323-L327
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331-L335



## [L-09] OWNER CAN INCREMENT BIDDING PRICE ANYTIME 

No check presents for input parameter percentage in function setMinimumBidIncrement(), that could be Any orbitary amount.

> **File : Auction.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331-L335


## [L-10] NO CHECK FOR RETURN VALUE OF IWETH TRANSFER



 