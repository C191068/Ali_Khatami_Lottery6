# Ali_Khatami_lottery6(Learning from the video of Patrick Collins)

### Implementing Chainlink Keepers (Checkupkeep method)

Now we will update our code so that the requestrandomchapion automatically happens <br>
using chainlink keepers <br>


Checkup function is basically checking to see is it time for us to get random number <br>

to update the recennt winner ansd sent them all the funds <br>

![k19](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/64abddf0-f534-48b3-bd39-d664c30d2861)

We shall import  /AutomationCompatibleInterface.sol so that we can implement both function checkUpkeep <br>

and promiseUpkeep <br>

![k20](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/0304feec-5a90-445e-b97e-45f936fc0fa3)

If we want we can import AutomationCompatible.sol or just AutomationCompatibleInterface <br>

![k21](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/0f986848-a35d-4db0-9046-2113389ba40a)

We have import the following in our code <br>

![k22](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/f13090db-3141-44b9-bf0c-f8c767179337)

Now akrklottery is inheritating both VRFConsumerBaseV2 and AutomationCompatible contract <br>

and this AutomationCompatible inheritance just makes sure that we add checkupkeep function <br>
and performupkeep function <br>

![k23](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/163f1944-4d29-4398-b64d-9d1276e03f84)

now we add as parameter to checkUpkeep function <br>

now this checkUpkeep bytes calldata allows us to specify really anything that we want <br>

when we call this checkUpkeep function <br>

Having this checkdata be of bytes means that we can even specify this to call other function <br>

there are lot of other things we can do by having this as input parameter of type bytes <br>


![k24](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/3bce0f78-fee8-4a2d-b50f-ed8382355ec1)

We are not gonna use this checkData part here <br>

![k25](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/f6aecf81-7d14-4bf2-aab6-6d879cb244f1)

here we gve an important comment <br>

if upkeepNeeded is true it is time to get random number <br>

The following should be true in order to return true <br>

1. our time interval should have passed <br>

2. The lottery should have at least one player and have some eth <br>

3. Our subscription is funded with link <br>

4. The lottery should be in an open state <br>



Similiar to how Chainlink VRF our subscription needed to be funded with link <br>

the same thing need to be happened for checkupkeep and kkepers to run <br>


Something we want to avoid when we are waiting for a random number to return <br>


![k26](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/e0007336-4f1e-4f47-b479-1046a5d48762)


when we requested a random champion we are technically in this weired state <br>

where we are waiting for a random number to returned <br>

And we shouldd not allow any new players to join <br>

So actually gonna create some state variables teloign us whether the lottery is <br>
open or not and while we are waiting for a random number to get back <br>
we will be in a closed or calculating state <br>


### ENUMS 



![k27](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/2dca388f-39f3-4c77-8856-c8f5ad57911c)

here we have created a new variable and we gonna set this to true if open otherwise false  <br>

we can have ton of different staete like pending, open ,closed and calculating <br>

a better way to keep track of the above state is to use Enum <br>

![k28](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/5bd50ed7-0e31-4937-9248-0bb7742c4464)

Defintion of Enum at this link https://docs.soliditylang.org/en/v0.8.17/types.html# <br>

![k29](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/477e6499-d9cb-4e42-8931-a9989a03541c)

we have created a new and our own data type and we gonna have it to be open or calculating <br>

We are kind of secretly creating uint256 where 0 equals open and 1 equals calculating <br>

![k30](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/b8bb7e30-2362-4db8-9b80-0092542c033c)

Here we ahve created a storage variable of type LotteryState <br>

![k31](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/9ae3d821-dde9-4d46-9e8d-de5855a16b57)

in our constructor right we launch this contract we should open up this lottery <br>
Now we know lotterystate is in open state and we  only want checkupkeep to work <br>
if the lottery state is in open state <br>

![k32](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/0aa00c3d-dfc4-4a0e-aa6e-ea1e55e9eb18)

additonally we want people only to enter if the lottery is open  <br>

now we will create anothe if statement and revert if the lottery is not open <br>

![k33](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/aab4b677-7e4b-4d01-8c41-2c71e8509d1b)

When we are requesting random words lets update the state to be calculating so that other <br>
people can jump in here <br>

So that nobody can enter our lottery to trigger new update <br>

![k34](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/f35aa6c0-3fa7-474f-bd07-32386afc4fa3)


And then ondce we fulfill after we pick our random winner we will give the above line of code <br>


![k35](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/9eac504e-c0e9-4ace-8845-9e54a6ebbcde)


After we pick a winner from s_participants we need to reset our participants array we use the above line<br>

thus we reset the Lotterystate and we reset our participants array <br>



```solidity

//SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@chainlink/contracts/src/v0.8/VRFConsumerBaseV2.sol";
import "@chainlink/contracts/src/v0.8/interfaces/VRFCoordinatorV2Interface.sol";
import "@chainlink/contracts/src/v0.8/AutomationCompatible.sol";

error akrkLottery_NotEnoughEthEntered();
error akrkLottery_TransferFailed();
error akrkLottery_NotOpen();

contract akrkLottery is VRFConsumerBaseV2, AutomationCompatible {
    //below we gonna pick minimum price and it gonna be storage variable
    //visibiliy will be private but it will be configurable
    //We will cover our both storage and non storage variables under state variables section

    /*New data type*/

   enum LotteryState {
        OPEN,
        CALCULATING
    }


    /* State variables */

    uint256 private immutable i_welcomeFee;
    //We probably also nedd to track of all the users who entered the lottery
    //participants is a storage variable because we gonna modify this a lot
    address payable[] private s_participants;

    VRFCoordinatorV2Interface private immutable i_vrfCoordinator;

    bytes32 private immutable i_gasLane;

    uint64 private immutable i_subscriptionId;

    uint32 private immutable i_callbackGaslimit;

    uint16 private constant REQUEST_CONFIRMATIONS = 3;

    uint32 private constant NUM_WORDS = 1;

    //Lottery variables 

    address private s_recentChampion;
    LotteryState private s_lotteryState;
    

    /* Events */

    event LotteryEnter(address indexed participants);

    event RequestedLotteryChampion(uint256 indexed requestId);

    event ChampionPicked(address indexed champion);

    // to configure it we st constructor below

    constructor(
        address vrfCoordinatorV2,
        uint256 welcomeFee,
        bytes32 gasLane,
        uint64 subscriptionId,
        uint32 callbackGaslimit
    ) VRFConsumerBaseV2(vrfCoordinatorV2) {
        i_welcomeFee = welcomeFee;

        i_vrfCoordinator = VRFCoordinatorV2Interface(vrfCoordinatorV2);

        i_gasLane = gasLane;

        i_subscriptionId = subscriptionId;

        i_callbackGaslimit = callbackGaslimit;

        s_lotteryState = LotteryState.OPEN;
    }

    //to enter the lottery we created a function below

    function enterLottery() public payable {
        if (msg.value < i_welcomeFee) {
            revert akrkLottery_NotEnoughEthEntered();
        }

        if(s_lotteryState != LotteryState.OPEN){
            revert akrkLottery_NotOpen();
        }

        s_participants.push(payable(msg.sender));

        emit LotteryEnter(msg.sender);
    }

    //this is the function that the Chainlink keeper nodes call 
    //they look for 'upkeepNeeded' to return true 

    function checkUpkeep(bytes calldata /* checkData */) external override 

    //to pick a random champion we created the function below
    //The below function is gonna be called by chainlink keepers network

    function requestRandomchampion() external {

        s_lotteryState = LotteryState.CALCULATING;
        uint256 requestId = i_vrfCoordinator.requestRandomWords(
            i_gasLane,
            i_subscriptionId,
            REQUEST_CONFIRMATIONS,
            i_callbackGaslimit,
            NUM_WORDS
        );

        emit RequestedLotteryChampion(requestId);
    }

    function fulfillRandomWords(
        uint256,
        /* requestId */ uint256[] memory randomWords
    ) internal override {
        uint256 indexofChampion = randomWords[0] % s_participants.length;

        address payable recentChampion = s_participants[indexofChampion];

        s_recentChampion = recentChampion;

        s_lotteryState = LotteryState.OPEN;

        s_participants = new address payable[](0);

        (bool success, ) = recentChampion.call{value: address(this).balance}("");

        if (!success) {
            revert akrkLottery_TransferFailed();
        }

        emit ChampionPicked(recentChampion);
    }

    //we want other users to see entrance fee so we created the function below
    /*View/Pure Function*/
    function getEntranceFee() public view returns (uint256) {
        return i_welcomeFee;
    }

    //to know who are in the participants array the function is created below
    function getParticipant(uint256 index) public view returns (address) {
        return s_participants[index];
    }

    function getRecentChampion() public view returns (address) {
        return s_recentChampion;
    }
}

```




Now we have learned about Enum now we will add to our checkUpkeep  <br>


We gonna check the below four things :
1. our time interval should have passed <br>

2. The lottery should have at least one player and have some eth <br>

3. Our subscription is funded with link <br>

4. The lottery should be in an open state <br>


If the above all are true checkUpkeep will be true and will trigger the chainlink keepers <br>

to request a new random champion <br>

![k36](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/2399d679-3469-4ed9-b136-860df4bca778)

This boolean state above gonna be true if the LotteryState is in open state <br>
And it will be false if Lotterystate is in any other state <br>

Now we check to make sure if our time interval is passed <br>



![k37](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/3f9039aa-82dd-40a5-aea2-c87dc97d987e)

In order to chcek the time we gonna use another solidity globally available variable <br>
known as block timestamp <br>

It returns the current timestamp of blockchain <br>

If enough time is passed we need to get the current blocktimestamp <br>
minus the lastblocktimestamp 


![k38](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/c564e7ed-52c9-4637-9e0c-b2dd92cd8453)


Now we will create a state variable that will keep track of previous <br>
blocktimestamp <br>


![k39](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/e1ac580e-b779-418d-8985-6b94c924aad8)


Right when we deploy this contract we will update this with current timestamp<br>

Now we gonnna need to check that differnce betwwn current timestamp and previous timestamp <br>

is greater than some interval <br>

Interval is gonna be some number in seconds of how long we want to wait between <br>

lottery runs <br>

![k40](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/89e864bb-19e6-4bda-8e50-114c2aed0bee)

Now we gonna add it to our constructor as well <br>

![k41](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/b073c5de-f8f8-4d5d-9ed0-5153e0a9d97a)

Now we gonna create another global variable <br>

![k42](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/a570b002-9dad-41d4-a2df-b72ec8944b17)

Then in our constructor ww will assign the above <br>

interval isn't gonna change after we set it <br>

![k43](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/b8cfd670-f6fc-4a29-bb82-cdcfb3dc1f55)

instead of making it storage variable we gonna make it immutable to save some gas <br>

![k44](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/0f4d5e69-5441-4ef3-a699-4c38bde4b7a4)

instead of making it storage variable we gonna make it immutable to save some gas <br>



![k45](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/2d16e1c6-da28-4648-b87a-6cf14a6b86b5)


now we will create boolean to check to see if enough time is passed <br>



![k46](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/c08828d2-9548-4550-bf3d-50f7507d0e6f)


Now we will check to see if we enough players <br>



![k47](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/7216397f-aa38-4e0e-9f55-28f9581d288a)

Now we will also see if we have a balance <br>

![k48](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/7a531ce3-0da7-412d-b569-856b8109359b)

Now we will take all these booleans and turn them into the return variable <br>
that we are looking for <br>

If the above returns true it is time to request a random number <br>

it is time to end the lottery <br>

if it is false it is not time yet ,it is not time to end the lottery up <br>

![k49](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/a30a9cde-8b3b-4c6b-9e18-09f88d4894e9)

now we will update the above function <br>

![k50](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/f67eeb8a-d76b-403d-97b3-0992815a44ff)

Since we have initialized boolean upkeepNeeded here <br>

![k51](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/04e63a9f-9433-423f-8c2d-eb2675331361)

We don't need to say what upkeepNeeded type is here <br>

Since it will automatically get returned <br>


![k52](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/9104e35e-d6dc-4031-abd1-21bc5be2fa59)

Now we have this checkupkeep to chcek to see if it is time to trigger picking <br>
the random winner of our lottery <br>


```solidity

//SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@chainlink/contracts/src/v0.8/VRFConsumerBaseV2.sol";
import "@chainlink/contracts/src/v0.8/interfaces/VRFCoordinatorV2Interface.sol";
import "@chainlink/contracts/src/v0.8/AutomationCompatible.sol";

error akrkLottery_NotEnoughEthEntered();
error akrkLottery_TransferFailed();
error akrkLottery_NotOpen();

contract akrkLottery is VRFConsumerBaseV2, AutomationCompatible {
    //below we gonna pick minimum price and it gonna be storage variable
    //visibiliy will be private but it will be configurable
    //We will cover our both storage and non storage variables under state variables section

    /*New data type*/

   enum LotteryState {
        OPEN,
        CALCULATING
    }

    /* State variables */

    uint256 private immutable i_welcomeFee;
    //We probably also nedd to track of all the users who entered the lottery
    //participants is a storage variable because we gonna modify this a lot
    address payable[] private s_participants;

    VRFCoordinatorV2Interface private immutable i_vrfCoordinator;

    bytes32 private immutable i_gasLane;

    uint64 private immutable i_subscriptionId;

    uint32 private immutable i_callbackGaslimit;

    uint16 private constant REQUEST_CONFIRMATIONS = 3;

    uint32 private constant NUM_WORDS = 1;

    //Lottery variables

    address private s_recentChampion;
    LotteryState private s_lotteryState;
    uint256 private s_lastTimeStamp;
    uint256 private immutable i_interval;

    /* Events */

    event LotteryEnter(address indexed participants);

    event RequestedLotteryChampion(uint256 indexed requestId);

    event ChampionPicked(address indexed champion);

    // to configure it we st constructor below

    constructor(
        address vrfCoordinatorV2,
        uint256 welcomeFee,
        bytes32 gasLane,
        uint64 subscriptionId,
        uint32 callbackGaslimit,
        uint256 interval
    ) VRFConsumerBaseV2(vrfCoordinatorV2) {
        i_welcomeFee = welcomeFee;

        i_vrfCoordinator = VRFCoordinatorV2Interface(vrfCoordinatorV2);

        i_gasLane = gasLane;

        i_subscriptionId = subscriptionId;

        i_callbackGaslimit = callbackGaslimit;

        s_lotteryState = LotteryState.OPEN;

        s_lastTimeStamp = block.timestamp;

        i_interval = interval;
    }

    //to enter the lottery we created a function below

    function enterLottery() public payable {
        if (msg.value < i_welcomeFee) {
            revert akrkLottery_NotEnoughEthEntered();
        }

        if (s_lotteryState != LotteryState.OPEN) {
            revert akrkLottery_NotOpen();
        }

        s_participants.push(payable(msg.sender));

        emit LotteryEnter(msg.sender);
    }

    //this is the function that the Chainlink keeper nodes call
    //they look for 'upkeepNeeded' to return true

    function checkUpkeep(
        bytes calldata /* checkData */
    ) external override returns (bool upkeepNeeded, bytes memory /*performData*/) {
        bool isOpen = (LotteryState.OPEN == s_lotteryState);
        bool timePassed = ((block.timestamp - s_lastTimeStamp) > i_interval);
        bool hasParticipants = (s_participants.length > 0);
        bool hasBalance = address(this).balance > 0;
        upkeepNeeded = (isOpen && timePassed && hasParticipants && hasBalance);
    }

    //to pick a random champion we created the function below
    //The below function is gonna be called by chainlink keepers network

    function requestRandomchampion() external {
        s_lotteryState = LotteryState.CALCULATING;
        uint256 requestId = i_vrfCoordinator.requestRandomWords(
            i_gasLane,
            i_subscriptionId,
            REQUEST_CONFIRMATIONS,
            i_callbackGaslimit,
            NUM_WORDS
        );

        emit RequestedLotteryChampion(requestId);
    }

    function fulfillRandomWords(
        uint256,
        /* requestId */ uint256[] memory randomWords
    ) internal override {
        uint256 indexofChampion = randomWords[0] % s_participants.length;

        address payable recentChampion = s_participants[indexofChampion];

        s_recentChampion = recentChampion;

        s_lotteryState = LotteryState.OPEN;

        s_participants = new address payable[](0);

        (bool success, ) = recentChampion.call{value: address(this).balance}("");

        if (!success) {
            revert akrkLottery_TransferFailed();
        }

        emit ChampionPicked(recentChampion);
    }

    //we want other users to see entrance fee so we created the function below
    /*View/Pure Function*/
    function getEntranceFee() public view returns (uint256) {
        return i_welcomeFee;
    }

    //to know who are in the participants array the function is created below
    function getParticipant(uint256 index) public view returns (address) {
        return s_participants[index];
    }

    function getRecentChampion() public view returns (address) {
        return s_recentChampion;
    }
}


```
































