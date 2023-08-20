# Ali_Khatami_lottery6(Learning from the video of Patrick Collins)

### Implementing Chainlink Keepers (Checkupkeep method)

Now we will update our code so that the requestrandomchapion automatically happens <br>
using chainlink keepers <br>


Checkup function is basically checking to see is it time for us to get random number <br>

to update the recennt winner ansd sent them all the funds <br>

![k19](https://github.com/C191068/Ali_Khatami_Lottery6/assets/89090776/64abddf0-f534-48b3-bd39-d664c30d2861)

We shall import  /AutomationCompatibleInterface.sol so that we can implement both function checkUpkeep <br>

function checkUpkeep <br>

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



















